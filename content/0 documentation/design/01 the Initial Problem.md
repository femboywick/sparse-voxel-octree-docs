---
tags:
  - diataxis/background
---
This article describes the initial problem, the motivation for it, and explains why this library had to be made. It explores some other solutions, and specifies an outline of what is being done to achieve the solution we chose. ^description
# todos  before publishing
- [ ] better name and maybe restructure
- [ ] add brief jump point system explanation
- [ ] quote, or paraphrase sparse voxel octrees page.
# Starting Off
## Initial Motivation: Space-Sim Example
I wanted to create a 6-degrees of freedom space simulator. After I managed to figure out some rudimentary physics using [[21 Libraries Used#godot rust|godot rust]] and a differential equations solver (for somewhat physically based movement), I wanted to move onto enemy AI. I quickly found that the available tools were not up to the job, so I spent some time looking at papers. And the output is this library (and this documentation!)
## The Available Tools: Navigation Meshes, and Why They Don't Work
in Godot, the game engine I wound up choosing, there are built-in tools for navigation (see [NavigationAgent3D](https://docs.godotengine.org/en/stable/classes/class_navigationagent3d.html) and [NavigationRegion3D](https://docs.godotengine.org/en/stable/classes/class_navigationregion3d.html#class-navigationregion3d))

The traditional way to navigate in 3D-space, the navmesh, is dependent on there being a floor that enemies, and the player, are attached to. Then, the navmesh is generated on each floor layer. Essentially, it creates a 2D representation of the floors within your level.

> [!quote] figure 1
> ![[navmesh.png]]
> a navigation mesh in godot, which supports slanted floors. source[^godot-navmesh]

[^godot-navmesh]: Taken from this [tutorial about navigation meshes in godot (stable)](https://docs.godotengine.org/en/stable/tutorials/navigation/navigation_using_navigationmeshes.html), [(permalink)](https://docs.godotengine.org/en/4.5/tutorials/navigation/navigation_using_navigationmeshes.html)

Navmeshes require floors in godot, and most engines I've encountered. in addition, navmeshes don't generally play well with obstacles above the floor.

This makes it unsuitable (as I understand it) for [[#full 3D navigation/path-finding|full 3D navigation]]. In a fully 3D setting (for example, a space-sim with 6 degrees of freedom) there's no guarantees that there will be a floor, or even a consistent up or down.
# Some more Details
## Problem Specifications
The problem described above can be written formally into specifications for what we want out of it. the [[#general]] section deals with the main outcomes that we want to achieve, whilst the [[#specific]] section details some problems along the way.
### General
#### Full 3D Navigation/Path-Finding
The agent should be able to navigate through space around obstacles. This means that, unlike in a navmesh which is focused on *surfaces*, we must focus on *volumes*[^git]. The result is that in, for instance, an asteroid field: the agent would be able to path-find through asteroids. ^volumetric-navigation

[^git]: Please see [[20 Sources#^git-issue|the github issue that started me on this journey]]. 
#### Untethered from the Ground
There should not have to be a ground for the enemy to be relative to, as in a navmesh.
### Specific
#### Voxelization
Typically, [[03 Constructing an SVO Part 1 - Partitioning, Voxelization and Building#input|triangle meshes]] are used for rendered geometry. Trying to path-find with a complex mesh, such as a scene, is very difficult. Instead, we shall turn to *voxels*.
You can think of the process as turning the *continuous* triangle mesh, where a vertex, edge, or face could be anywhere in space, to a *discrete* voxel grid, where each voxel has a consistent placement.
A voxel representation needs to be made from the input geometry. This resulting voxel representation will then be navigated through. 
#### Path-Finding Should Be Optimal in Relation to the World
Whatever method is used to generate the voxelization of the scene (see [[methods of representing obstacles in 3D]]), the final path should be optimal relative to the world. 
This means that if a voxel octree, like in this crate, is used to represent traverse-able space, the path output will be relative to the octree. See [[02 Sparse Voxel Octrees - an Introduction#sub-optimal pathing|suboptimal pathing]] for a more detailed explanation
## The *Applicable* Available Tools
When researching 3D navigation, I came across two solutions. the faster, "state-of-the-art" *3D-jump-point-system*, and the slightly more well-researched *sparse voxel octrees*.
### 3D Jump Point System (JPS)
the jump point system[^1] 

[^1]:  [The Jump Point Search Pathfinding System in 3D](https://ojs.aaai.org/index.php/SOCS/article/download/21762/21526/25805)

### Sparse Voxel Octrees
see [[02 Sparse Voxel Octrees - an Introduction|sparse voxel octrees]]

## Producing a Solution
The final goal is to be able to navigate through 3D space, whilst avoiding obstacles in the way. 

To achieve this, we'll employ a [[02 Sparse Voxel Octrees - an Introduction|sparse voxel octree]], first [[03 Constructing an SVO Part 1 - Partitioning, Voxelization and Building|generating a surface voxelization]], then [[flood filling to find the valid explore-able air voxels]]. Once this is created, it can be stored and serialised for future use, as the current implementation is single-core, therefore slow. Afterwards, the sparse voxel octree can be navigated through, to find an optimal path ([[02 Sparse Voxel Octrees - an Introduction#sub-optimal pathing|relative to the octree]]). Then, we can optimise our path-finding algorithm, to find an optimal path relative to world space.

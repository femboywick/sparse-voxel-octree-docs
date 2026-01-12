---
tags:
  - diataxis/background
---
this article describes the initial problem, the motivation for it, and explains why this library had to be made. it explores some other solutions, and specifies an outline of what is being done to achieve the solution we chose. ^description
# todos  before publishing
- [ ] better name and maybe restructure
- [ ] add brief jump point system explanation
- [ ] quote, or paraphrase sparse voxel octrees page.
# starting off
## initial motivation: spacesim example
i wanted to create a 6-degrees of freedom space simulator. after i managed to figure out some rudimentary physics using [[libraries used#godot rust|godot rust]] and a differential equations solver (for somewhat physically based movement), i wanted to move onto enemy ai. i quickly found that the available tools were not up to the job, so i spent some time looking at papers. and the output is this library (and this documentation!)
## the available tools: navigation meshes, and why they don't work
in godot, the game engine i wound up choosing, there are built in tools for navigation (see [NavigationAgent3D](https://docs.godotengine.org/en/stable/classes/class_navigationagent3d.html) and [NavigationRegion3D](https://docs.godotengine.org/en/stable/classes/class_navigationregion3d.html#class-navigationregion3d))

the traditional way to navigate in 3D-space, the navmesh, is dependant on there being a floor that enemies, and the player, are attached to. then, the navmesh is generated on each floor layer. essentially, it creates a 2D representation of the floors within your level.

> [!quote] figure 1
> ![[navmesh.png]]
> a navigation mesh in godot, which supports slanted floors. [source](https://docs.godotengine.org/en/stable/tutorials/navigation/navigation_using_navigationmeshes.html)

navmeshes require floors in godot, and most engines I've encountered. in addition, navmeshes don't generally play well with obstacles above the floor.

this makes it unsuitable (as i understand it) for [[#full 3D navigation/path-finding|full 3D navigation]], as in a fully 3D setting (for example, a space-sim with 6 degrees of freedom) there's no guarantees that there will be a floor, or even a consistent up or down.
# some more details
## problem specifications
the problem described above can be written formally into specifications for what we want out of it. the [[#general]] section deals with the main outcomes that we want to achieve, whilst the [[#specific]] section details some problems along the way.
### general
#### full 3D navigation/path-finding
the agent should be able to navigate through space around obstacles. this means that, unlike in a navmesh which is focused on *surfaces*, we must focus on *volumes*. the result is that in, for instance, an asteroid field: the agent would be able to path-find through asteroids. 
#### untethered from the ground
there should not have to be a ground for the enemy to be relative to, as in a navmesh.
### specific
#### voxelization
typically, [[02 constructing an SVO part 1 - voxelization#input|triangle meshes]] are used for rendered geometry. trying to path-find with a complex mesh, such as a scene, is very difficult. instead we shall turn to *voxels*. think of the process as turning the continuous triangle mesh, where any vertex could be anywhere in space, to a discrete voxel grid, where each voxel has a consistent placing.
a voxel representation needs to be made from the input geometry. this resulting voxel grid will then be navigated through. 
#### path-finding should be optimal in relation to the world
whatever method is used to generate the voxelization of the scene (see [[methods of representing obstacles in 3D]]), the final path should be optimal relative to the world. 
this means that if a voxel octree, like in this crate, is used to represent traverse-able space, the path output will be relative to the octree. see [[01 sparse voxel octrees - an introduction#sub-optimal pathing|sub-optimal pathing]] for a more detailed explanation
## the *applicable* available tools
when researching 3D navigation, I came across two solutions. the faster, "state-of-the-art" *3D-jump-point-system*, and the slightly more researched *sparse voxel octrees*.
### 3D jump point system (JPS)
the jump point system[^1] 

[^1]:  [The Jump Point Search Pathfinding System in 3D](https://ojs.aaai.org/index.php/SOCS/article/download/21762/21526/25805)

### sparse voxel octrees
see [[01 sparse voxel octrees - an introduction|sparse voxel octrees]]

## producing a solution
the final goal is to be able to navigate through 3D space, whilst avoiding obstacles in the way. 

to achieve this, we'll employ a [[01 sparse voxel octrees - an introduction|sparse voxel octree]], first [[02 constructing an SVO part 1 - voxelization|generating a surface voxelization]], then [[flood filling to find the valid explore-able air voxels]]. once this is created, it can be stored and serialised for future use, as the current implementation is single-core, therefore slow. afterwards, the sparse voxel octree can be navigated through, to find an optimal path ([[01 sparse voxel octrees - an introduction#sub-optimal pathing|relative to the octree]]). then, we can optimise our path-finding algorithm, to find a more optimal path relative to world space.

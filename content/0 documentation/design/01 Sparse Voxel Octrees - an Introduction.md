---
title: "01 Sparse Voxel Octrees: an Introduction"
tags:
  - diataxis/background
---
This article serves as an introduction to sparse voxel octrees, the main data structure used. Voxel octrees are an efficient way to store large voxelized scenes, which we implement path-finding through. ^description
### Why Voxels?
in a navmesh, all the tiles are convex. This makes it quite convenient to path-find through, as:
> Two points in the same \[convex\] tile can always be connected by a straight line that does not intersect any obstacles
> [[Sources#^efficient-pathfinding-thesis|ref. 4 thesis: page 16]]
## Voxel Grids Vs Octrees
### Whats a Voxel Grid?
A voxel grid is a 3D grid, where each space is occupied by a filled or empty voxel. This can be seen on the left of figure 1, where the red cells are full voxels, and everything else is empty. 
The initial approach one might take to storing this voxel grid is to store all the full or empty voxels in a list. To path-find, one would apply A\* treating every empty voxel as traverse-able, similar to how you could apply A\* to a 2D grid.
> [!quote] figure 1
> ![[voxel grid vs octree.png]]
> on the left, a voxel grid decomposition of the scene, each voxel is the same size. on the right, a voxel octree decomposition. both sides feature a path found using

### Whats the Difference Between Grids and Octrees?
A voxel octree is an optimised representation of voxels in a region of space. 
The main premise of an octree is that large empty sections of space can be represented using a single voxel at best rather than many voxels of the same size. As seen in figure 1, a large region of empty space at the top right is stored using a single voxel in the octree (right), and many voxels in the voxel grid (left).

See the [[Sources#^gameaipro|game AI pro 3 chapter by Daniel Brewer]] for a brief overview of sparse voxel octrees, their construction, and path-finding through them.
## A Brief Overview
### What Is a Voxel Octree?
A voxel octree is a [tree](https://en.wikipedia.org/wiki/Tree_(abstract_data_type)), with each node either containing 8 children (hence **oct**-tree), or no children at all. 

If a node has 8 children, you can think of it as being "partially full". If a node contains no children, it is considered a *leaf* node. A leaf node represents an empty or full voxel. An example of a leaf node is the top left node in the third/second level.

These nodes are stored in layers, from the root (first level node in figure 2), to the maximum depth. With each layer you add, you double the resolution on every side of the octree. (for example, from 3 layers which is $4\times4\times4$, to 4 layers which is $8\times8\times8$)

> [!quote] figure 2
> ![[octree.png]]
> a voxel octree, from top to bottom it displays the root node, followed by 1st layer, followed by the 2nd) TODO: check if these layer numberings are right
> [source](https://commons.wikimedia.org/wiki/File:Octree2.svg)

### Whats a *Sparse* Voxel Octree?
The main difference between a voxel octree and a sparse one is that in an SVO, a non-leaf node may not have 8 valid children. 
What makes it "sparse" is that each node can have 0 *to* 8 children, as opposed to 0 *or* 8. This means that empty children are not stored.
### How Does this Apply to Path-Finding?
Recall that, in an octree, a large empty space can be stored as one voxel. This larger voxel can be navigated through without obstacle. A lot less calculations are necessary to path-find through an octree representation, where many voxels are large and can be passed through.
## Some Specifics
### Node Storage
For our representation of a node, we must store two things:
- position
- children

Mainly for convenience, we store the position of each node within the SVO. it is possible to store this either as a 64-bit [[10 morton coding|Morton code]][^morton], or a vector.

[^morton]: With a 64-bit Morton code, 63 bits are actually used, with 21 bits allocated per coordinate.

From a node, we must be able to access all of its children. A first approach might be to store a reference to every single child.


> [!info] How do we store a reference?
> It is possible to model an octree where each node contains pointer(s) to its children. However, it is also possible (and a bit more effective in my opinion) to instead store an index to node(s) within a layer. So to store this 'reference', the layer[^link] and index are written. We call this a *link*.
> Later on, we will use these *links* to store references to parents and neighbours

[^link]: If you were not concerned with the neighbours or parents of nodes, you would not need to store the *layer* for each of these links, as the layer of a node's children is just that nodes layer +1. 
  in fact, it would be a bit more efficient to use just an index instead of a link for the first child of a node

Regardless of if an octree is sparse or not, we can make a simple optimisation. Instead of storing a link to *each* child, we can store a link to the *first* child and calculate the offset. 
If an octree is not sparse, a non-leaf node must have 8 children. So finding the nth child is trivial. For example, if you wanted to find the 3rd child and the first child has an index of 82, you would add an offset of 2 to get the final index 84. This becomes a little more complex with an SVO, as a node is not guaranteed to have 8 children. 

One possible solution, as used in [[Sources#^ooc-svo-construction-src|the implementation of out-of-core construction of sparse voxel octrees]], is to store an index offset for each child. So, for every node you would store each of the 8 potential children's offset from the first child. If the child is empty (and therefore not stored), you would have a special value (say, the 8-bit limit) that indicates that the child is invalid.

The solution I ended up choosing was similar to the one proposed in [[Sources#^efficient-svos|reference 3]]. Each node, instead of containing a list of offset indices, contains an 8-bit mask, where each bit represents if the child is full (1), or empty (0). From here, it is somewhat trivial[^8bitalgo] to find any given child. ^8bit

[^8bitalgo]: TODO: write a link or text here about the algorithm

so, finally, for our node we have: a position, a first child link, and a

## Limitations
### Suboptimal Pathing

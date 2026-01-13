---
title: "01 sparse voxel octrees: an introduction"
tags:
  - diataxis/background
---
this article serves as an introduction to sparse voxel octrees, the main data structure used. voxel octrees are an efficient way to store large voxelised scenes, which we implement path-finding through. ^description
### why voxels?
in a navmesh, all the tiles are convex. this makes it quite convenient to path-find through, as:
> two points in the same \[convex\] tile can always be connected by a straight line that does not intersect any obstacles
> [[sources#^efficient-pathfinding-thesis|ref. 4 thesis: page 16]]
## voxel grids vs octrees
### whats a voxel grid?
a voxel grid is a 3D grid, where each space is occupied by a filled or empty voxel. this can be seen on the left of figure 1, where the red cells are full voxels, and everything else is empty. 
the initial approach one might take to storing this voxel grid is to store all the full or empty voxels in a list. to path-find, one would apply A\* treating every empty voxel as traverse-able, similar to how you could apply A\* to a 2D grid.
> [!quote] figure 1
> ![[voxel grid vs octree.png]]
> on the left, a voxel grid decomposition of the scene, each voxel is the same size. on the right, a voxel octree decomposition. both sides feature a path found using

### whats the difference between grids and octrees?
a voxel octree is an optimised representation of voxels in a region of space. 
the main premise of an octree is that large empty sections of space can be represented using a single voxel at best rather than many voxels of the same size. as seen in figure 1, a large region of empty space at the top right is stored using a single voxel in the octree (right), and many voxels in the voxel grid (left).

see the [[sources#^gameaipro|game ai pro 3 chapter by daniel brewer]] for a brief overview of sparse voxel octrees, their construction, and path-finding through them.
## a brief overview
### what is a voxel octree?
a voxel octree is a [tree](https://en.wikipedia.org/wiki/Tree_(abstract_data_type)), with each node either containing 8 children (hence **oct**-tree), or no children at all. 

if a node has 8 children, you can think of it as being "partially full". if a node contains no children, it is considered a *leaf* node. a leaf node represents an empty or full voxel. an example of a leaf node is the top left node in the third/second level.

these nodes are stored in layers, from the root (first level node in figure 2), to the maximum depth. with each layer you add, you double the resolution on every side of the octree. (for example, from 3 layers which is $4\times4\times4$, to 4 layers which is $8\times8\times8$)

> [!quote] figure 2
> ![[octree.png]]
> a voxel octree, from top to bottom it displays the root node, followed by 1st layer, followed by the 2nd) TODO: check if these layer numberings are right
> [source](https://commons.wikimedia.org/wiki/File:Octree2.svg)

### whats a *sparse* voxel octree?
the main difference between a voxel octree and a sparse one is that in an SVO, a non-leaf node may not have 8 valid children. 
what makes it "sparse" is that each node can have 0 *to* 8 children, as opposed to 0 *or* 8. this means that empty children are not stored.
### how does this apply to path-finding?
recall that, in an octree, a large empty space can be stored as one voxel. this larger voxel can be navigated through without obstacle. a lot less calculations are necessary to path-find through an octree representation, where many voxels are large and can be passed through.
## some specifics
### node storage
for our representation of a node, we must store two things:
- position
- children

mainly for convenience, we store the position of each node within the SVO. it is possible to store this either as a 64-bit [[10 morton coding|morton code]] [^morton], or a vector.

[^morton]: with a 64-bit morton code, 63 bits are actually used, with 21 bits allocated per coordinate.

from a node, we must be able to access all of its children. a first approach might be to store a reference to every single child.


> [!info] how do we store a reference?
> it is possible to model an octree where each node contains pointer(s) to its children. however, it is also possible (and a bit more effective in my opinion) to instead store an index to node(s) within a layer. so to store this 'reference', the layer[^link] and index are written. we call this a *link*.
later on, we will use these *links* to store references to parents and neighbours

[^link]: if you were not concerned with the neighbours or parents of nodes, you would not need to store the *layer* for each of these links, as the layer of a node's children is just that nodes layer +1. 
  in fact, it would be a bit more efficient to use just an index instead of a link for the first child of a node

regardless of if an octree is sparse or not, we can make a simple optimisation. instead of storing a link to *each* child, we can store a link to the *first* child and calculate the offset. 
if an octree is not sparse, a non-leaf node must have 8 children. so finding the nth child is trivial. for example, if you wanted to find the 3rd child and the first child has an index of 82, you would add an offset of 2 to get the final index 84. this becomes a little more complex with an SVO, as a node is not guaranteed to have 8 children. 

one possible solution, as used in [[sources#^ooc-svo-construction-src|the implementation of out-of-core construction of sparse voxel octrees]], is to store an index offset for each child. so, for every node you would store each of the 8 potential children's offset from the first child. if the child is empty (and therefore not stored), you would have a special value (say, the 8-bit limit) that indicates that the child is invalid.

the solution i ended up choosing was similar to the one proposed in [[sources#^efficient-svos|reference 3]]. each node, instead of containing a list of offset indices, contains an 8-bit mask, where each bit represents if the child is full (1), or empty (0). from here, it is somewhat trivial[^8bitalgo] to find any given child.

[^8bitalgo]: TODO: write a link or text here about the algorithm

so, finally, for our node we have: a position, a first child link, and a

## limitations
### sub-optimal pathing

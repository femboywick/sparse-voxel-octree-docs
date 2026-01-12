---
title: "01 sparse voxel octrees: an introduction"
tags:
  - diataxis/background
---
this article serves as an introduction to sparse voxel octrees, the main data structure used. voxel octrees are an efficient way to store large voxelised scenes, which we implement path-finding through. ^description
### voxel grids vs octrees
> [!quote] figure 1
> ![[voxel grid vs octree.png]]
> on the left, a voxel grid decomposition of the scene, each voxel is the same size. on the right, a voxel octree decomposition.

an octree is an optimised representation of voxels in a region of space. the initial approach one might take to storing voxels is to store all the full or empty voxels in a list. this can be seen on the left of figure 1. to path-find, one would apply A\* or another path-finding algorithm, treating every empty voxel as traverse-able, similar to how you could apply A\* to a 2D grid.

the main premise of an octree is that large empty sections of space can be represented using a single voxel at best rather than many voxels of the same size. as seen in figure 1, a large region of empty space at the top right is stored using a single voxel in the octree (right), and many voxels in the voxel grid (left).

see the [[sources#reference 1|game ai pro 3 chapter by daniel brewer]] for a brief overview of sparse voxel octrees, their construction, and path-finding through them.
## a brief overview
### whats a voxel octree?
a voxel octree is a [tree](https://en.wikipedia.org/wiki/Tree_(abstract_data_type)), with each node either containing 8 children (hence **oct**-tree), or no children at all. if a node contains no children, it is considered a *leaf* node. a leaf node represents an empty or full voxel. 
these nodes are stored in layers, from the root (first node in figure 2), to the maximum depth. with each layer you add, you double the resolution of every side of the octree.

> [!quote] figure 2
> ![[octree.png]]
> a voxel octree, from top to bottom it displays the root node, followed by 1st layer, followed by the 2nd) TODO: check if these layer numberings are right
> [source](https://commons.wikimedia.org/wiki/File:Octree2.svg)

### whats a sparse voxel octree?
the main difference between a voxel octree and a sparse one is that in an SVO, a node may not have 8 valid children. 
what makes it "sparse" is that each node can have 0-8 children, as opposed to 0 or 8. this means that empty children are not stored, and each layer you store less and less empty space.
### how does this apply to path-finding?
recall that, in an octree, a large empty space can be stored as one voxel. this large voxel can be navigated through without obstacle. a lot less calculations are necessary to path-find through an octree representation, where many voxels are large and can be passed through without
## some specifics
### node storage

## limitations
### sub-optimal pathing

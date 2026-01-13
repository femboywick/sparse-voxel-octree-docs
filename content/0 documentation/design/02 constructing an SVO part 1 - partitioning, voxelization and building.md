---
tags:
  - diataxis/background
title: "02 constructing an SVO part 1: partitioning, voxelization and building"
---
as part of the construction of the [[01 sparse voxel octrees - an introduction|sparse voxel octree]], an intermediary voxel grid needs to be built. this article explores how to create such a grid, and the motivation necessary to move onto the [[next step, flood-filling]]. it also describes how a space is partitioned into multiple voxel grids depending on available memory, and then finally how these grids are built into the final octree structure.^description
## voxel grids
a [[01 sparse voxel octrees - an introduction#whats a voxel grid?|voxel grid]] is the simplest way to represent voxels in space. it stores every filled voxel's position, where each voxel is the same size.

for an SVO, a voxel grid is constructed at the resolution of the lowest layer of the octree. from this intermediary,  we can build a sparse octree from the bottom up.

## morton order
## input
- [ ] TODO: write about triangle meshes, and getting them from godot
collision meshes, which work faster with convex shapes, can be made from many simple convex representations of a potentially concave whole.
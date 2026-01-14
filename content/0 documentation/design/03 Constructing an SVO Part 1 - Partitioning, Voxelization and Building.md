---
tags:
  - diataxis/background
title: "02 Constructing an SVO Part 1: Partitioning, Voxelization and Building"
---
As part of the construction of the [[02 Sparse Voxel Octrees - an Introduction|sparse voxel octree]], an intermediary voxel grid needs to be built. This article explores how to create such a grid, and the motivation necessary to move onto the [[next step, flood-filling]]. It also describes how a space is partitioned into multiple voxel grids depending on available memory, and then finally how these grids are built into the final octree structure. ^description
## Voxel Grids
A [[02 Sparse Voxel Octrees - an Introduction#whats a voxel grid?|voxel grid]] is the simplest way to represent voxels in space. it stores every filled voxel's position, where each voxel is the same size.

For an SVO, a voxel grid is constructed at the resolution of the lowest layer of the octree. From this intermediary, we can build a sparse octree from the bottom up.

## Morton Order
## Input
- [ ] TODO: write about triangle meshes, and getting them from godot
collision meshes, which work faster with convex shapes, can be made from many simple convex representations of a potentially concave whole.
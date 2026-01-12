---
tags:
  - diataxis/background
title: "02 constructing an SVO part 1: voxelization"
---
as part of the construction of the [[01 sparse voxel octrees - an introduction|sparse voxel octree]], an intermediary voxel grid needs to be built. this article explores how to create such a grid, and the motivation necessary to move onto the [[next step, flood-filling]]. ^description
## voxel grids
a voxel grid is the simplest way to represent voxels in space. it stores every filled voxel's position, where each voxel is the same size.

for an SVO, a voxel grid is constructed at the resolution of the lowest layer of the octree. from this intermediary, 

## morton order
## input
TODO: write about triangle meshes, and getting them from godot
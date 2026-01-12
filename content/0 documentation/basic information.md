---
tags:
  - diataxis/background
---
the goal of this library is to navigate in a [[00 the initial problem#full 3D navigation|fully 3D way]], around obstacles and without requiring a floor like a navmesh does. it presents a sparse voxel octree, where a leaf node describes traverse-able space.

---
## glossary
- SVO -> sparse voxel octree
- voxel -> volume element, is the 3D equivalent of a pixel (picture element).
- resolution -> how many voxels wide something is. 
	- each layer added to a voxel octree doubles the resolution.
	- for instance, a voxel octree with 3 layers (1 root node, with 8 children, each with another 8) would have a resolution of 4 voxels across.
- nodes:
	- leaf node ->
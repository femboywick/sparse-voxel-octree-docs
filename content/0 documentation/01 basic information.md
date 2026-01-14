---
tags:
  - diataxis/background
---
The goal of this library is to navigate in a [[01 the Initial Problem#full 3D navigation|fully 3D way]], around obstacles and without requiring a floor like a navmesh does. It presents a sparse voxel octree, where a leaf node describes traverse-able space.

---
## Glossary
- SVO -> sparse voxel octree
- Voxel -> volume element, is the 3D equivalent of a pixel (picture element).
- Resolution -> how many voxels wide something is. 
	- Each layer added to a voxel octree doubles the resolution.
	- For instance, a voxel octree with 3 layers (1 root node, with 8 children, each with another 8) would have a resolution of 4 voxels across.
- nodes:
	- leaf node ->
---
tags:
  - diataxis/background
---
Designing this library was a task that involved many sub-goals along the way. The purpose of this chapter is to document some of the problems I faced and how they were solved.

This chapter contains information about the core theory behind the library. Whilst it may not be as easily applicable as other parts of this documentation, if you would like to contribute, or even make your own library, this is the place to start. ^description

## Questions, and Which Article Answers Them
- Question: What's a "Sparse Voxel Octree"?
	- Answer: see [[02 Sparse Voxel Octrees - an Introduction]]
- Question: What's the point of an SVO? Why not just use a navmesh?
	- Answer: see [[01 the Initial Problem]]
- Question: How is this applied?
	- Answer: if you're looking for *cognition*, that is to say to *understand* how to make and use an SVO, see this three part series
		- [[03 Constructing an SVO Part 1 - Partitioning, Voxelization and Building]]
		- todo: add links here
	- Answer: if you're looking for a *tutorial* on how to make your own SVO, see [[20 let's create - a sparse voxel octree in rust]]


---

This section is mainly concerned with [[02 Sparse Voxel Octrees - an Introduction|SVO]]s, constructing them, converting an initial SVO into a use-able traverse-able 3D navigation graph, and finally path-finding through the graph.

## [[01 the Initial Problem]]
![[01 the Initial Problem#^description]] 

## [[02 Sparse Voxel Octrees - an Introduction]]
![[02 Sparse Voxel Octrees - an Introduction#^description]]

## [[03 Constructing an SVO Part 1 - Partitioning, Voxelization and Building]]
![[03 Constructing an SVO Part 1 - Partitioning, Voxelization and Building#^description]]

## [[04 Constructing an SVO Part 2 - Flood-Fill]]

## [[05 Part 3 Path-Finding Through an SVO]]

---

The next section of this chapter has some further information about some inner workings.

## [[10 Morton Coding]]
![[10 Morton Coding#^description]]

---

Finally, we provide a basic set of tutorials for creating your own SVO.

## [[20 let's create - a sparse voxel octree in rust]]
![[20 let's create - a sparse voxel octree in rust#^description]]
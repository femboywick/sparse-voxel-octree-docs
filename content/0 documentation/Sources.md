## The GitHub Issue that Started It All
in my initial search for how to do 3D navigation in godot, I found [this](https://github.com/godotengine/godot-proposals/issues/7504) issue. in it, @smix8 pointed out reference 1. From the chapter, I found a few papers, which eventually lead onto a myriad of other papers. The ones I've mainly been referencing whilst writing my code are listed below

TODO:
- [ ] list all papers
- [ ] check if the massonnat papers are correctly dated

## Papers
### Reference 1 ^gameaipro
[3D Flight Navigation Using Sparse Voxel Octrees](https://www.gameaipro.com/GameAIPro3/GameAIPro3_Chapter21_3D_Flight_Navigation_Using_Sparse_Voxel_Octrees.pdf) (2018) - D. Brewer
- chapter in game ai pro 3
### Reference 2 ^ooc-svo-construction
[Out-of-Core Construction of Sparse Voxel Octrees](https://graphics.cs.kuleuven.be/publications/BLD14OCCSVO/BLD14OCCSVO_paper.pdf) (2014) - J. Baert, A. Lagae and Ph. Dutré
- see [[#source code]] ^ooc-svo-construction-src
### Reference 3 ^efficient-svos
[Efficient Sparse Voxel Octrees – Analysis, Extensions, and Implementation](https://research.nvidia.com/sites/default/files/pubs/2010-02_Efficient-Sparse-Voxel/laine2010tr1_paper.pdf) (2010) - S. Laine, T. Karras
### Reference 4 ^efficient-pathfinding
[Efficient Octree-based 3D Pathfinding](https://www.sable.mcgill.ca/~clump/papers/massonnat-24-efficient.pdf) (2024) - Q. Massonnat, C. Verbrugge
#### Reference 4, Thesis ^efficient-pathfinding-thesis
[Efficient Octree-based 3D Pathfinding (thesis)](https://escholarship.mcgill.ca/downloads/xw42nf656?locale=en) (2023) - Q. Massonnat

## Source Code
- 2a:  [out of core SVO builder](https://github.com/Forceflow/ooc_svo_builder/tree/main) TODO: credits for these
- 2b: [libmorton](https://github.com/Forceflow/libmorton)
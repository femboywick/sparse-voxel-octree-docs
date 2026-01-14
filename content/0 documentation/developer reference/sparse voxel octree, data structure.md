---
tags:
  - diataxis/reference
---
the `SvoIntermediary` type is (currently) the main type involved in storing a sparse voxel octree. 

## the basic constructor: `SvoConstructor`

to construct a SVO using the rust library, you will use a `SvoConstructor`. this struct is responsible for storing the basic information about the SVO, and using it to construct a surface voxelization of the input mesh.

the `new()` function takes in:
- a `Vec` of [[libraries used#parry3d|parry3d]] `Triangle`s
- an amount of layers to generate (final resolution is calculated by $2*2^{layers}$)
- a memory limit (measured in megabytes)
	- this implementation is [out of core](https://en.wikipedia.org/wiki/External_memory_algorithm) as per [[sources#^ooc-svo-construction|reference 2]]. please note that *out of core* does not necessarily mean *multi-threaded*.
	- as part of [[#`SvoPartitioner`]]

once a `SvoConstructor` has been created, `build()` can be called on it to output a final `SvoIntermediary`.

below features a simple example for calling the constructor
```rust
let layers = 3;
let memory_limit = 1000;
let triangles = get_triangles()

let constructor = SvoConstructor::new(layers, memory_limit, triangles);

constructor.build() // returns an SvoIntermediary, and sonsumes constructor
```
---
## storage of SVOs
### `SvoIntermediary`

the struct is defined as follows:
```rust
pub struct SvoIntermediary {
    pub layers: Vec<Vec<SvoNodeIntermediary>>,
    pub current_indices: Vec<usize>, // this is solely to keep track of the amount of nodes.
}
```

`layers` is a list of layers, with each layer being a list of nodes. these are stored in [[10 morton coding|morton order]].
each node is an [[#`SvoNodeIntermediary`]].
#### creating and using your own `SvoIntermediary`
to create an SVO,

### `SvoNodeIntermediary`
### `SvoLink`
---
## what `SvoConstructor` is actually doing

when `build()` is called on the constructor, 
### `SvoPartitioner`
### `SvoVoxelizer`
### `SvoBuilder`
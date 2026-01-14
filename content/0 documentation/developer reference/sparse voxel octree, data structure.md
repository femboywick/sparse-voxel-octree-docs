---
tags:
  - diataxis/reference
---
The `SvoIntermediary` type is (currently) the main type involved in storing a sparse voxel octree. 

## The Basic Constructor: `SvoConstructor`

To construct an SVO using the rust library, you will use a `SvoConstructor`. This struct is responsible for storing the basic information about the SVO, and using it to construct a surface voxelization of the input mesh.

the `new()` function takes in:
- a `Vec` of [[21 Libraries Used#parry3d|parry3d]] `Triangle`s
- An amount of layers to generate (final resolution is calculated by $2*2^{layers}$)
- A memory limit (measured in megabytes)
	- This implementation is [out of core](https://en.wikipedia.org/wiki/External_memory_algorithm) as per [[20 Sources#^ooc-svo-construction|reference 2]]. Please note that *out of core* does not necessarily mean *multi-threaded*.
	- As part of [[#`SvoPartitioner`]], the voxel grid is split up into powers of 8.

Once a `SvoConstructor` has been created, `build()` can be called on it to output a final `SvoIntermediary`.

Below features a simple example for calling the constructor
```rust
let layers = 5; // resolution of 64
let memory_limit = 1000;
let triangles = get_triangles();

let constructor = SvoConstructor::new(layers, memory_limit, triangles);

constructor.build() // returns an SvoIntermediary, and consumes constructor
```
---

## Storage of SVOs
### `SvoIntermediary`

> [!info] Definition
> ```rust
> pub struct SvoIntermediary {
>     pub layers: Vec<Vec<SvoNodeIntermediary>>,
>     pub current_indices: Vec<usize>, // this is solely to keep track of the amount of nodes.
> }
> ```

`layers` is a list of layers, with each layer being a list of nodes. These are stored in [[10 Morton Coding|Morton order]].
Each node is an [[#`SvoNodeIntermediary`]]. 

### `SvoNodeIntermediary`
> [!info] Definition
>```rust
>pub struct SvoNodeIntermediary {
>     pub position: Point3<u16>,
>     pub first_child: Option<SvoLink>,
>     pub children_offsets: SvoNodeChildrenIntermediary,
>}
>```

As specified in [[02 Sparse Voxel Octrees - an Introduction#Node Storage|the node storage section]], for every node within the tree we shall store the position, first child, and a bit-mask. Whether the node is a leaf or not is determined from the `children_offsets`. Below is an example of the construction and storage of a node, from `SvoBuilder`.
```rust
let node = SvoNodeIntermediary::leaf(position);

self.buffers[layer].push_back(Some(node));
self.refine_buffers(layer);
```
### `SvoLink`
> [!info] Definition
> ```rust
>pub struct SvoLink {
>	pub layer: u8,
>	pub node_index: u32, // 32 bits should be enough for a full 1024x1024 grid
>}
> ```


The layer and index (relative to specified layer) is stored in the `SvoLink` struct. Below contains an example of how you could obtain an `SvoLink` from an `SvoIntermediary`, use it to find an `SvoNodeIntermediary`, and then check if that node is a leaf.
```rust
let Some(node_link) = svo.get_node_at_point(layer_position, layer) else {
    return;
};

let node = svo.get_node(&node_link);

if node.is_leaf() {
    println!("node is a leaf");
}
```
### `SvoNodeChildrenIntermediary`
> [!info] Definition 
> ```rust
> pub struct SvoNodeChildrenIntermediary {
>     pub child_mask: u8,
> }
> ```

The `child_mask` stored is an 8-bit mask. For the theory, see [[02 Sparse Voxel Octrees - an Introduction#^8bit|this section of an article in design]]. In practise, we store each child as a bit in the mask. When there are no children (IE, a leaf node), the mask is 0. For convenience, an `is_leaf()` function is provided that checks this. A function for this is also included in `SvoNodeIntermediary`. 

Also included are a few functions that will get children of the a node, given its `first_child`. These then return an `SvoLink`, or a `Vec` of them.

An example taken from `SvoBuilder`:
```rust
let buffer: &[Option<SvoNodeIntermediary>] = ..

let child_offsets = SvoNodeChildrenIntermediary::leaf();
// check every node in a buffer
for (index, child) in buffer.iter().enumerate() {
    if child.is_some() {
        ..
        // if the child is non-empty, set index as filled
        child_offsets.set_index_filled(index as u8);
    }
}

// note: this has been simplified, excludes the code for adding first_child to node.
```

---
## What `SvoConstructor` Is Actually Doing

When `build()` is called on the constructor, 
### `SvoPartitioner`
### `SvoVoxelizer`
### `SvoBuilder`
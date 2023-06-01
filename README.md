# [slicedvec](https://docs.rs/slicedvec)

In a recent project, I found myself repeatedly pushing smallish, run-time sized random strings into a `Vec<Vec<f32>>` data structure. This was iterated with subsequent application of `swap_remove` or `nth_element` and `truncate` 10's of thousands to millions of times. Profiling indicated that the allocator was consuming a small but significant amount of time. This is because each inner `Vec<T>` allocates and deallocates memory on the heap, which is slow and causes fragmentation. So, I decided I needed a way to store all these uniform-length strings of random numbers in a single vector. The result is [this crate](https://docs.rs/slicedvec) which defines `SlicedVec<T>` and `SlicedSlab<T>` data structures. `SlicedVec<T>` emulates aspects of `Vec<Vec<T>>`, or more acturately a pseudo-`Vec<&[T]>`, but using a single `Vec<T>` for storage. The intention is to make this data structure as comfortable as using `Vec<T>` but having it return slices rather than single values. The length of the slices is fixed at intialization. A `SlicedSlab<T>` has segmented storage, but acts as a slab-allocator. Each insertion returns a key that can be used to retreive the data. The slots indicated by the keys can be released and new insertions will always be placed as far forward in the storage as possible. If no slots are open, the storage is extended. Of course, none of this is necessary if the length of the segments is known at compile time. But the length is a quantity of interest in the work, so I prefer to set it at initialization, not at compile time. I split this off into its own crate thinking it might be useful to others. Please raise an issue if you would like some added feature or would like to collaborate.

  

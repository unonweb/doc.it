# LINKS

- https://doc.rust-lang.org/book/ch08-00-common-collections.html

# ARRAYS & VECTORS

```rust
let vec_of_strings: Vec<&str> = vec!["hello", "world"];
let array_of_strings: [&str; 2] = ["foo", "bar"];
```

# VECTORS

Vectors are re-sizable arrays. Like slices, their size is not known at compile time, but they can grow or shrink at any time. A vector is represented using 3 parameters:

- pointer to the data
- length
- capacity

The capacity indicates how much memory is reserved for the vector. The vector can grow as long as the length is smaller than the capacity. When this threshold needs to be surpassed, the vector is reallocated with a larger capacity.

## create

```rust
// vector creation with Vec::new() method
let mut v: Vec<i32> = Vec::new();
v.push(10);
v.push(20);
// we added a type annotation here. Because we aren’t inserting any values into this vector, Rust doesn’t know what kind of elements we intend to store

let v = vec![1, 2, 3]; 
// the macro allows the use of the array-like literal syntax with initial values // Rust will infer the type of value
```

## convert

```rust
// Iterators can be collected into vectors
let collected_iterator: Vec<i32> = (0..10).collect();
```

## iteration

```rust
for x in xs.iter() {
    println!("> {}", x);
}

// A `Vector` can also be iterated over while the iteration
// count is enumerated in a separate variable (`i`)
for (i, x) in xs.iter().enumerate() {
    println!("In position {} we have value {}", i, x);
}

// Thanks to `iter_mut`, mutable `Vector`s can also be iterated
// over in a way that allows modifying each value
for x in xs.iter_mut() {
    *x *= 3;
}
```
## instance methods

- https://doc.rust-lang.org/std/vec/struct.Vec.html#implementations

```rust
xs.push(4); // needs to be mutable
xs.pop(); // removes the last element from the vector and returns it

xs.get(3); // returns Option<T>
```

### split_off()

```rust
let mut vec = vec!['a', 'b', 'c'];
let vec2 = vec.split_off(1);
assert_eq!(vec, ['a']);
assert_eq!(vec2, ['b', 'c']);
```

## methods from Deref

- https://doc.rust-lang.org/std/vec/struct.Vec.html#deref-methods-%5BT%5D

### split_at()

- https://doc.rust-lang.org/std/vec/struct.Vec.html#method.split_at
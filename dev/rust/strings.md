# STRINGS

- https://www.ameyalokare.com/rust/2017/10/12/rust-str-vs-String.html

## strings and functions

- **`&str` for parameters; `String` for return types**
  This always works, and has no borrow complications, but is sometimes inefficient

**receive `String`:**

`String` can be a better choice: if the caller already has a `String` they don't need to hold on to, they can give it to you. 

If they don't, they'll have to clone or create one -- but if you take a `&str`, you'll _always_ have to do so.

**return `&str`**:

- You cannot return `&str` unless you were passed in a `&str`
  Because you if you do, then you are creating a reference to a variable local to the function and that reference will become invalid one you return from the function
- A `&str` return type to a function can only be a string that was already referenced (or part of something being referenced) as _input_ to the function. A good example would be matching a string against a regex and returning the matching part: The returned `&str` is _a part of_ the input! Or a function on a struct representing – say – a person, and returning their name. The name is already part of the input. (In both cases, I assume the input is a _reference_, too.)

`&str` 
- as a function parameter
- if you want a read-only view of a string

## compare String and str

```rust
// This code works because `my_string` is a string literal (str).
// However, if `my_string` is a `String` object, this code will not compile.
fn main() {
    let my_string = "hello";
​
    match my_string {
        "hello" => println!("Hello, world!"),
        "goodbye" => println!("Goodbye, world!"),
        _ => println!("Neither hello nor goodbye!"),
    }
}
```

```rust
// Here we need to convert `my_string` (a String object) into a string slice (str)
fn main() {
    let my_string: String = String::from("hello");
​
    match my_string.as_str() {
        "hello" => println!("Hello, world!"),
        "goodbye" => println!("Goodbye, world!"),
        _ => println!("Neither hello nor goodbye!"),
    }
}
```
## String

  - growable, heap-allocated, UTF-8 encoded string
  - use for a mutable and dynamic string that can be modified and resized at runtime
  - maintains a **length _and_ a capacity**

```rust
let mut my_string = String::new();
```

```rust
let mut s = String::from("Hello, Rust!");
println!("{}", s.capacity()); // prints 12
s.push_str("Here I come!");
println!("{}", s.len()); // prints 24
```

- `&String` can be _coerced_ to `&str`

```rust
fn main() {
    let s = String::from("Hello, Rust!");
    foo(&s);
}

fn foo(s: &str) {
    println!("{}", s);
}
```

- `foo()` can take either string slices or borrowed `String`s, which is super convenient. 
- As such, **you almost never need to deal with `&String`**
- Prefer `&str` as a function parameter or if you want a read-only view of a string
## str

- https://stackoverflow.com/questions/24158114/what-are-the-differences-between-rusts-string-and-str/24159933#24159933
- immutable fixed-length string _somewhere_ in memory
- can live on the heap, stack or in the binary
- only has a `len()` method
- You can only ever interact with `str` as a _borrowed_ type aka `&str`
- `&str`: This is called a _string slice_, an immutable view of a string. This is the preferred way to pass strings around, as we shall see.

```rust
let s = "Hello, Rust!";
println!("{}", s.capacity()); // compile error: no method named `capacity` found for type `&str`
println!("{}", s.len()); // prints 12
```

- `String` owns the data it represents and is responsible for its memory allocation and deallocation.
- `str` (String Slice) is a borrowed reference to a portion of an existing string or string literal, and it does not own the data.

## instance methods
### .is_empty()

```rust
fn main() {
    let my_string: String = String::from("Hello, World!");

    if !my_string.is_empty() {
        println!("The string is set and not empty: {}", my_string);
    } else {
        println!("The string is either not set or empty.");
    }
}
```

### .parse()

```rust
// convert string to i64
let input = match input.trim().parse() {
    Ok(parsed_input) => parsed_input,
    Err(_) => continue,
};
```
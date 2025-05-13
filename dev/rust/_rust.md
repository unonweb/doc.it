# LINKS

- Why Rust is a significant development in programming languages
  https://www.youtube.com/watch?v=IwjlCxwcuIc
- https://crates.io/
- https://itnext.io/shell-scripting-in-rust-2bdb8c738c94
- https://doc.rust-lang.org/cargo/guide/
- The Rust Programming Language
  https://doc.rust-lang.org/book
- A Gentle Introduction To Rust
  https://stevedonovan.github.io/rust-gentle-intro/1-basics.html

# RUST
## managing shared mutable state

For the past 50 years a defining problem in computer programming has been **managing shared mutable state**. That is data which can change, but is used in multiple places. Functional languages have attempted to avoid this by preventing mutability. Object-oriented languages have attempted to avoid it by encapsulating state in classes to limit sharing. Neither of these approaches solves the root problem, as a result, it has cropped up in a wide variety of  surprising ways: security vulnerabilities, the need for garbage collection, data races, null pointer exceptions, and performance problems. 

The first programming language to directly solve this problem is Rust. Because of this, code written in Rust can have:

- memory safety without garbage collection
- concurrency without race conditions
- performance without compromising safety

# IMPORTS

```rust
use std::env; 
// bring the parent module into scope rather than the function
// By doing so, we can easily use other functions from std::env

fn main() {
    let args: Vec<String> = env::args().collect();
}
```

# CLI

**cargo**

- two hyphens to indicate the following arguments are for our program

```bash
cargo run -- <searchstring> <file>
```

## std::env::args

- returns an iterator of the command line arguments
- Iterators produce a series of values
- We can call the `collect` method on an iterator to turn it into a collection, such as a vector


# MATCH

- The arms of a match must cover all the possible values

```rust
match VALUE {
    PATTERN => EXPRESSION,
    PATTERN => EXPRESSION,
    PATTERN => EXPRESSION,
}
```


```rust
fn main() {
    let number = 13;
    match number {
        1 => println!("One!"),
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        13..=19 => println!("A teen"),
        _ => println!("Ain't special"),
    }

    let boolean = true;
    let binary = match boolean {
        false => 0,
        true => 1,
    };
}
```

## option enum

The `Option` type or `Option<T>` type is an `enum` type just like `Result` with two possible variants.

- `None` → to indicate failure with no value
- `Some(T)` → a value with type `T`

```rust
fn main() {
    let text = "Hello, World!";
    
    let character_option = text.chars().nth(15);
    
    // using match for Option type
    let character = match character_option {
        None => "empty".to_string(),
        Some(c) => c.to_string()
    };
    
    println!("Character at index 15 is {}", character);
}
```

## difference between result and option enum

`Option` enum can return `None`, which can indicate failure.

However, sometimes it is essential to express why an operation failed. Thus, we have the `Result` enum, which gives us the `Err` with the reason behind the failure of the operation.

In short,

- `Option` is about `Some` or `None` (value or no value)
- `Result` is about `Ok` or `Err` (result or error result)

# HOW TO

## read file

- https://doc.rust-lang.org/stable/rust-by-example/std_misc/file/read_lines.html?highlight=read_lines

# PACKAGES & CRATES

- https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html


# TEST

## range

`min..=max` is a range inclusive

```rust
if (min..=max).contains(&input) {
      return input;
  } else {
      println!("\n That choice isn't allowed!  \n");
      continue;
  };
```
# TYPES

## gerenics

As a convention,
- `T`, `U` are used for arbitrary types
- `K`, `V` are used for key-value types
- `E` is used for error type


# ERRORS

```rust
use std::fs::File;

fn main() {
    let data_result = File::open("data.txt");

    // using match for Result type
    let data_file = match data_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the data file: {:?}", error),
    };

    println!("Data file", data_file);
}
```

## recoverable errors

- https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

## error propagation

- https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html#propagating-errors
- A shortcut for propagating errors: the `?` operator

Using match can get very verbose:

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

Using the ? operator:

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("hello.txt")?;
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;
    Ok(username)
}
```

- If the value of the `Result` is an `Ok`, the value inside the `Ok` will get returned from this expression, and the program will continue.
- If the value is an `Err`, the `Err` will be returned from the whole function as if we had used the `return` keyword so the error value gets propagated to the calling code.

Even shorter:

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username = String::new();

    File::open("hello.txt")?.read_to_string(&mut username)?;

    Ok(username)
}
```
# ITERATORS

## collect

turn iterator into vector

```rust
fn main() {
    let args: Vec<String> = env::args().collect();
}
```

We can use the `collect` function to create many kinds of collections, so we explicitly annotate the type of `args` to specify that we want a vector of strings.

Although you very rarely need to annotate types in Rust, `collect` is one function you do often need to annotate because Rust isn’t able to infer the kind of collection you want.

# MISC

## naming conventions

- https://rust-lang.github.io/api-guidelines/naming.html

## documentation

- https://doc.rust-lang.org/rust-by-example/meta/doc.html
- https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html

- `//!` indicates module-level or crate-level documentation.

### `#[doc]` attribute

- `///` is syntax sugar for `#[doc]`. This means that these two are the same:

```rust
/// This is a doc comment.
#[doc = r" This is a doc comment."]
```

- In most cases, `///` is easier to use than `#[doc]`
### section: examples

- Note the heading is *"Examples"*, plural. Be consistent with the plurality here. Even if there is only one example, the consistency helps with searching.

```rust
/// # Examples
///
/// ```ignore
/// let bummer = this_code_wont_work(); // but it's an illustrative example.
/// ```
```

### examples

```rust
/// Summary line -> what is this
///
/// Longer description of what is returned, or semantics regarding the type.
/// ...
///
/// # Examples
///
/// ```
/// <some-rust-code></some-rust-code>
/// ```
```

```rust
#![crate_name = "doc"]

/// A human being is represented here
pub struct Person {
    /// A person must have a name, no matter how much Juliet may hate it
    name: String,
}

impl Person {
    /// Creates a person with the given name.
    ///
    /// # Examples
    ///
    /// ```
    /// // You can have rust code between fences inside the comments
    /// // If you pass --test to `rustdoc`, it will even test it for you!
    /// use doc::Person;
    /// let person = Person::new("name");
    /// ```
    pub fn new(name: &str) -> Person {
        Person {
            name: name.to_string(),
        }
    }

    /// Gives a friendly hello!
    ///
    /// Says "Hello, [name](Person::name)" to the `Person` it is called on.
    pub fn hello(&self) {
        println!("Hello, {}!", self.name);
    }
}

fn main() {
    let john = Person::new("John");

    john.hello();
}
```
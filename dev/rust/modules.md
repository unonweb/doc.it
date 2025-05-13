# LINKS

- https://doc.rust-lang.org/book/ch07-02-defining-modules-to-control-scope-and-privacy.html
- https://medium.com/codex/rust-modules-and-project-structure-832404a33e2e

# MODULES

- files and folders are treated in the same way - as modules 
  - but folder require a `mod.rs` file inside
- **crate roots**: 
  `src/main.rs` and `src/lib`
  The contents of either of these two files form a module named `crate` at the root of the crate’s module structure, known as the _module tree_.
  `crate` is always at the top of our module tree, and any modules we declare are going to be children of `crate`.

```rust
mod cat; // look for
// either cat.rs in the same directory
// OR alternatively a directory named cat with a mod.rs file within it
```



```rust
mod my_struct; // Import the module code, placing it into it's own namespace
use crate::my_struct::MyStruct; // Map the fully qualified struct declaration
```

## modules in one file

```rust
// src/lib.rs

mod english {
    mod greetings {
    }

    mod farewells {
    }
}

mod japanese {
    mod greetings {
    }

    mod farewells {
    }
}
```
## modules in separate files

```sh
├── Cargo.lock
├── Cargo.toml
├── src
│   ├── english
│   │   ├── farewells.rs
│   │   ├── greetings.rs
│   │   └── mod.rs
│   ├── japanese
│   │   ├── farewells.rs
│   │   ├── greetings.rs
│   │   └── mod.rs
│   └── lib.rs
└── target
    └── debug
        ├── build
        ├── deps
        ├── examples
        ├── libphrases-a7448e02a0468eaa.rlib
        └── native
```

`src/lib.rs` is our crate root, and looks like this:

```rust
mod english;
mod japanese;
```
## module in directory

- think of `mod.rs` file as defining the interface to your module

### basic

```rust
// src/foo/mod.rs
pub mod another; // include another file
pub struct MyStruct {} // mod defined in mod.rs directly
```

```rust
// src/main.rs
mod foo;
use crate::foo::MyStruct;
use crate::foo::another::Another;
```
### better

```rust
// src/foo/mod.rs
mod another; // remove the 'pub' modifier
pub use another::Another; // add 'use' to map 'Another' directly into 'foo' and make it public
pub struct MyStruct {}
```

```rust
// src/main.rs
mod foo;
use crate::foo::{MyStruct,Another};
```

## lib.rs and main.rs

Our package now has two crates: `src/lib.rs` and `src/main.rs`. This pattern is quite common for executable crates: most functionality is in a library crate, and the executable crate uses that library. This way, other programs can also use the library crate, and it’s also a nice separation of concerns.
## visibility

- Everything _inside_ a module (ie, a file or subfolder within the `/src` folder) can access _anything else_ within that module.
- Everything _outside_ a module can _only_ access public members of that module.
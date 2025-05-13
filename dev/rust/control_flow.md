# LOOPS

## break

**break and return a value**

```rust
fn main() {
    let mut x = 0;
    let v = loop {
        x += 1;
        if x == 13 {
            break "found the 13";
        }
    };
    println!("from loop: {}", v);
}
```

# CONDITIONS

```rust
let s = if condition {
    "first"
} else {
    "second"
}.to_string();
```

**ternary expression**

```rust
let x = 42;
// Rust's ternary expression
let v = if x < 42 { -1 } else { 1 };
```
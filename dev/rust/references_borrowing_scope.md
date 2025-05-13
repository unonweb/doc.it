
## scope

```rust
// error[E0597]: `x` does not live long enough
fn main() {
    let r;
    {
        let x = 5;
        r = &x;
    }
    println!("r: {r}");
}
```

- `r` references `x`
- but `r` lives longer than `x`
- won’t compile because the value that `r` is referring to has gone out of scope before we try to use it.

## lifetimes

- https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html
- https://dhghomon.github.io/easy_rust/Chapter_40.html
- **Every reference in Rust has a lifetime**
  **which is the scope for which that reference is valid**
- Most of the time, lifetimes are implicit and inferred
- The main aim of lifetimes is to **prevent _dangling references_**, which cause a program to reference data other than the data it’s intended to reference.
- You only need to think about lifetimes with references.
  This is because references can't live longer than the object they come from.

### lifetime elision

- https://doc.rust-lang.org/nomicon/lifetime-elision.html


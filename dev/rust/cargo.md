- https://doc.rust-lang.org/cargo/guide/

**Add dependency to Cargo.toml**
```sh
cargo add console
```

**Import dependency**
```rust
use console;
fn main() {
    println!("This is {} neat", console::style("quite").cyan());
}
```

**Build**
```sh
cargo run # downloads and builds all dependencies
```
# RESULT

- choose `expect` rather than `unwrap` and give more context about why the operation is expected to always succeed.
## .unwrap()

- If the `Result` value is the `Ok` variant, `unwrap` will return the value inside the `Ok`. 
- If the `Result` is the `Err` variant, `unwrap` will call the `panic!` macro for us.

## .expect()

- similiar to `unwrap()` 
- lets us also choose the `panic!` error message

## result enum

The `Result<T, E>` type returns either a value or an error in Rust. It is an `enum` type with two possible variants.

- `Ok(T)` → operation succeeded with value `T`
- `Err(E)` → operation failed with an error `E`

```rust
match data_result {
    Ok(file) => file,
    Err(error) => panic!("Problem opening the data file: {:?}", error),
 };
```

# FUNCTIONS

- https://thoughtbot.com/blog/rust-doesn-t-have-named-arguments-so-what
- https://www.thecodedmessage.com/posts/default-params/
- https://doc.rust-lang.org/rust-by-example/fn.html

# STRUCTS

- is a collection of fields
- is a custom data type
- related values that make up a meaningful group
- Its definition is like a blueprint for a compiler on how to layout the fields in memory nearby each other.
- **static methods**
  methods that belong to a type itself are called using the `::` operator
- **instance methods** 
  methods that belong to an instance of a type are called using the `.` operator.

```rust
struct SumArgs {
  n1: i32,
  n2: i32,
}
```

```rust
fn main() {
  let args = SumArgs { n1: 2, n2: 3 }; // struct init; no constructor!
  let sum = add_numbers(&args);
  println!("{} + {} = {}", args.n1, args.n2, sum);
}
```

## tuple structs

For situations where you don’t necessarily care about the field names, Rust supports so called _tuple structs_. We can convert our simple SumArgs struct above to a tuple struct like so —

```rust
struct SumArgs(i32, i32);
```

```rust
fn main() {
  let args = SumArgs(2, 3); // <-- tuple struct init
  let sum = add_numbers(&args);
  println!("{} + {} = {}", args.0, args.1, sum);
}
```

The initialisation of the tuple struct is much simpler, but **what we gain in simplicity we lose in readability** when accessing the tuple struct fields. Tuple structs are accessed via their position in the struct initialisation. That is, `.0` and `.1` in this case.

```rust
fn add_numbers(args: &SumArgs) -> i32 {
  args.0 + args.1 // <-- tuple struct access
}
```

We can use Rust’s destructuring syntax to assign more meaningful names to the fields, as seen here in this minor change to the `add_numbers` function —

```rust
fn add_numbers(args: &SumArgs) -> i32 {
  match args {
    SumArgs(n1, n2) => n1 + n2, // use destructuring for more meaningful names
  }
}
```

## associated functions 

- similar to declaring a static or class method in other languages
- Use `impl` to define all functions that we want to be _associated_ with a struct
- It is then scoped to the struct type
- Use the `::` syntax to access an associated function

```rust
// struct def

struct SumArgs {
  n1: i32,
  n2: i32,
}
```

```rust
// before: independed function

fn add_numbers(args: &SumArgs) -> i32 {
  args.n1 + args.n2
}

fn main() {
  let args = SumArgs { n1: 2, n2: 3 };
  let sum = add_numbers(&args);
  println!("{} + {} = {}", args.n1, args.n2, sum);
}
```

Now let's move the `add_numbers` function onto the `SumArgs` struct. Given the tight dependence between the values and the function itself, this seems to make sense.

```rust
// after: associated function

impl SumArgs {
  fn add_numbers(args: &SumArgs) -> i32 {
    args.n1 + args.n2
  }
}

fn main() {
  let args = SumArgs { n1: 2, n2: 3 };
  let sum = SumArgs::add_numbers(&args);
  println!("{} + {} = {}", args.n1, args.n2, sum);
}
```

It is now scoped to the struct type, but it has no other connection. That is, we can’t access the fields, so we’re still passing the `args` object to the function. We haven’t gained very much at all, but we can do a lot better than this!

## constructors

- By convention use a function called `new` associated with the struct
- **return**: The `new` function returns `Self`, which is similar to `this` in other languages.
  The type name `SumArgs` could be used in place of `Self`, but this is a common pattern and nice syntactic sugar.
- **init**: To create a new `SumArgs` object we call the associated function, passing through to the two parameters.
- similar to a _static factory_ pattern for creating objects in other languages

```rust
impl SumArgs {
  // constructor
  fn new(n1: i32, n2: i32) -> Self {
    Self { n1, n2 }
  }
}

fn main() {
  let args = SumArgs::new(2, 3);
  let sum = SumArgs::add_numbers(&args);
  println!("{} + {} = {}", args.n1, args.n2, sum);
}
```

## methods

A method is an associated function (ie, declared against a struct) that takes a special parameter `self` as the first argument.

Let’s see what our `add_numbers` function looks like if we convert it to a _method_ —

```rust
impl SumArgs {
  // constructor
  fn new(n1: i32, n2: i32) -> Self {
    Self { n1, n2 }
  }
  // method
  fn add_numbers(&self) -> i32 {
    self.n1 + self.n2
  }
}

fn main() {
  let args = SumArgs::new(2, 3);
  let sum = args.add_numbers(); // changed to dot syntax!
  println!("{} + {} = {}", args.n1, args.n2, sum);
}
```

- The `add_numbers` method takes the special parameter `&self`, which gives us access to the structs fields (ie, `n1` and `n2`), via the `self` argument.
  Note that `self` is actually syntactic sugar for `self: Self`. 
  Also note that here we’re borrowing self because of the `&` prefixing the type.
- Calling a method requires an _instance_ of the type. Since we previously created the `args` object, we can use the method access syntax `.` to call `add_numbers`.
- Lastly, although the method declaration takes one argument (`self`), Rust knows that we mean the instance on which the method was called, so there is no need to explicitly pass in `args`.
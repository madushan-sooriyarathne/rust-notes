 # Closures

- Closures are anonymous functions you can save in a variable or pass as arguments to other functions.
- Closures can be created in one place and then call the closure to evaluate it in a different context.
- Unlike functions, closures can capture values from the scope in which they’re defined.

### Syntax

```rust
let closure = | x | {
	println!("{}", x);
	x
}; // closures are statments. thus requires the semicolan
```

We Can avoid using curly brackets if the closure has only one statement inside it.

```rust
let another_closure = | x | x;
```

We can separate multiple arguments by a comma. 

```rust
let closure_with_multiple_arguments = | x, y | x + y;
```

Types of closures are automatically inferred by rust as closures are not intended to use by any outside codes. However, we can implicitly add type annotations to closures. 

```rust
let closure_with_types = | x: u32, y: u32 | -> u32 {
	println!("Calculating the total of {} and {}", x, y);
	x + y
}
```
Rust automatically infer the types based on the usage of the closure. (Most of the time based on the first use)


##  Storing Closures Using Generic Parameters

All closures implement at least one of the traits: `Fn`, `FnMut`, or `FnOnce`.

```rust
struct SomeStruct<T> where T: Fn(u32) -> u32, {
	closure: T,
}
```


## Capturing the Environment with Closures

Closures have an additional capability that functions don’t have: they can capture their environment and access variables from the scope in which they’re defined. Capturing values from the environment can be done in 3 ways. 

1. `fnOnce` - Consumes the variables it captures from its enclosing scope. Takes the ownership of the value. thus, this closure can only be used once. 
2. `FnMut`  - Can change the environment because it mutably borrows values.
3.  `Fn`  - Borrows values from the environment immutably.

Rust infers which trait to use based on how the closure uses the values from the environment.

```rust
let x = vec![10, 20, 30];

let equal_to_x = move | y | y == x; 
// Using move keyword before parameter list, we force
// closure to use fnOnce trait

println!("Value of x is {:?}", x); 
// cannot use x here, as it's moved to obove clousre. Gives a error

assert_eq!(equal_to_x(vec![10, 20, 30]));

```

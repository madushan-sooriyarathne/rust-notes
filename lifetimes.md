 # Rust lifetimes

- Special feature available in rust to make sure there are no memory leaks or dangling references. 
- Most of the time lifetimes for references are implicit and inferred. 


### Problem

```rust
{
    let r;
    {
        let x = 5; // lifetime of x begins here1
        r = &x; // referance to x variable
    } // lifetimes of x ends here as it goes out of scope

    println!("r: {}", r); // gives and error due to unavialable reference.
}
```

### Fix

```rust
    let x = 5; // lifetime of x starts here
    let r = &x; 

	prinln!("r: {}", r); // lifetime of x still valids here. so no problem
```

## Generic lifetime parameter

### Problem

```rust
fn longest(x: &str, y: &str) -> &str {
	if x.len() > y.len() {
		return x;
	} else {
		return y;
	}
} 
```

This function gives us an error because the Rust compiler doesn't know the availability/lifetime of given parameter references at compile time. And it cannot tell to which parameter the return reference is related to. 

### Fix

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
	if x.len() > y.len() {
		return x;
	} else {
		return y;
	}
}
```

Here we annotate the functions' parameter and return with **generic lifetime parameter annotation**, which tells the compiler that the return references have the same lifetime as parameters. 

When we specify the lifetime parameters in the function signature, we’re not changing the lifetimes of any values passed in or returned. Rather, we’re specifying that the borrow checker should reject any values that don’t adhere to these constraints

> lifetime annotation on function parameters are called **input lifetimes** and lifetime annotation on return values are called **output lifetimes**.


```rust
// This code is accepted by rust compiler because all the referances passed in have a lifetime same or more than return values lifetime. 
fn main() {
	let str1 = String::from("Hello, ");
	{
		let str2 = String::from("World from Rust"); // lifetime begins here
		let result = longest(str1.as_str(), str2.as_str()); // return value ("result") lifetime starts here
		println!("Longest text is {}", result); // return value used here
	} // return value & str2 lifetime ends here
} // str1 lifetime ends here
```


```rust 
	// this code fails becuase result variable (return referance) has more lifetime than str2
fn main() {
	let str1 = String::from("Hello, "); // str1 lifetime starts here
	let result; // result lifetime starts here
	{
		let str2 = String::from("World from rust!"); // str2 lifetime starts here
		result = longest(str1.as_str(), str2.as_str()); 
	} // str2 lifetimes ends here
	println!("Longest text is {}", result); // result (Return reference) is used here
}
```

##### Note
- Lifetime parameter for the return type always needs to match the lifetime parameter for one of the parameters.

## Lifetime Annotations in Struct Definitions

Structs can have references instead of values. But lifetime annotation is a must when using references.

```rust 
struct Person<'a> {
	name: &'a str,
}

fn main() {
	let first_name = String::from("Madushan");
	let madushan = Person{name: first_name.as_str()};
}
```

## Lifetime Annotations in Method Definitions
Lifetime names for struct fields always need to be declared after the `impl` keyword and then used after the struct’s name, because those lifetimes are part of the struct’s type.

```rust 
struct Person<'a> {
	name: &'a str,
}

impl<'a> Person<'a> {
	fn get_name(&self) -> &str { // lifetime of output value will be implicitly infered (3rd lifetime elision rule)
		// snip...
	}
}
```


## Lifetime Elision Rules

Lifetime elision rules are a set of cases that the rust compiler will consider, and if our code fits these cases, the compiler will infer the lifetimes implicitly. There are 3 elision rules. 1st one on input lifetimes and 2nd & 3rd on output lifetimes. These rules apply to  `fn`  definitions as well as  `impl`  blocks.

 1. Each parameter that is a reference gets its own lifetime parameter.
 2. If there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters.
 3. If there are multiple input lifetime parameters, but one of them is `&self` or `&mut self` because this is a method, the lifetime of `self`, is assigned to all output lifetime parameters.

```rust
// rule 1
fn some_function<'a>(para1: &'a str) // snip...
fn some_function<'a, 'b>(para1: &'a str, para2: &'b str) // snip...	
fn some_function<'a, 'b, 'c>(para1: &'a str, para2: &'b str, para3: &'c str) // snip...

// rule 2
fn some_fn<'a>(para1: &'a str) -> &'a str // snip..

// rule 3
// below code will not work as this is implicitly done by compiler
// but it gives the idea 
fn some_method<'a>(&'a self) -> &'a str // snip..
```

## The Static Lifetime
**'static** is a special lifetime annotation which means that this reference  _can_ live for the entire duration of the program

 - All string slices has 'static lifetime `let str1: &'static str = "Rust is awesome";`


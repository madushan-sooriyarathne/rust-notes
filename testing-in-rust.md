 # Testing in Rust

 - Test functions in rust annotated with `#[test]` attribute. 
 - When we run the `cargo test` command, Rust builds a test runner binary that runs the functions annotated with the `#[test]` attribute and reports on whether each test function passes or fails.
 - Tests in rust always run in separate threads. Thus when one test function failed / panicked, that doesn't stop other test functions from being run. 

```rust
#[cfg(test)]
mod tests {
	#[test]
	fn it_works() {
		assert_eq!(2 + 2, 4); // This test will pass
	}
	
	#[test]
	fn it_fails() {
		assert(false); // This test will fail and test function will panic
	}
}
```

 - We can use `assert!()`, `assert_eq!()`, `assert_ne!()` macros in-side the test functions. 
 - Also, we can use `#[should_panic]` attribute along with `#[test]` attribute to test _panicable_ state. By adding a `#[should_panic(expected="panic message here")]` expected parameter, we can specify which panic type we are expecting. 
 - Also, We can return `Result<T, E>` enum from the test function. When returned the error test will be failed. (example below)


```rust
#[test]
fn it_works() -> Result<() , String> {
	if(2 + 2 == 4) {
		return Ok(());
	} else {
		return Err(String::from("Test failed!"))
	}
}
``` 

 # Testing in Rust

## Unit Tests
 - Unit tests are written in same files that our normal code lives. We annotate test module with `#[cfg(test)] ` attibute in-order to tell rust compiler that these are tests. Rust compiler builds test binaries using **test functions in these test modules & any helper functions** when we use `cargo test` and when we run `cargo build` / `cargo build --release` compiler does not include these tests in the final binary. 
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
 - By adding `#[ignore]` attribute to test function, We can tell rust compiler to skip this test. 
 - Also, We can return `Result<T, E>` enum from the test function. When returned the `Err` test will be failed. (example below)


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
### Running Tests on Private Functions
Rust's privacy policy allow us to write test functions on private functions naturally as test functions are meant to test the functions and not to run it. 
```rust
#[cfg(test)]
mod test {
	use super::*; // get all the functions outside this module to this scope. 
	
	#[test]
	fn test_on_pvt_fn() {
		assert_eq!(5, get_five()); 
		// get_five is a private functions but here it does not give any errors
	}	
}

// a private function
fn get_five() -> u32 {
	5
}
```

## Integration Tests
- Integration tests are located in a sperate directory named `\tests` (alongside `\src` directory)  and they can only call the function in the public API of our code.
- Purpose of Integration tests is to test whether many parts of the library work together correctly. 
- Integration test can only be run on public functions that exposed from `src/lib.rs`. It cannot be run on funtions in `src/main.rs` as main.rs make binary crates and it menat to be run on it's own.  

```rust
// adder/src/lib.rs

// public function. can test using integration tests. 
pub fn get_five() -> u32 {
	5
}

// private function. cannot test using integration tests. 
fn pvt_get_five() -> u32 {
	5
}
```

```rust
// adder/tests/integration_test.rs
use adder; // imoprt the poject content. this case project name is adder.

#[test]
fn test_get_five() {
	assert_eq!(5, adder::get_five());
}

```

### Submodules in Integration tests
Each file in `/tests` directory will run as a separate crate by default. This helps to separtes the scopes. However, when we need to use helper functions that might be use in multiple integration test files, we can put that helper functions in a sperate file named `mod.rs` within a directory that is in `/tests` directory. Putting these helper function files in to seperate directory is importent as if those are in the `/tests` directory, those will appear on the `cargo test` output

```rust
/// adder/tests/common/mod.rs
pub fn helper_fn() {
	// snip..
}
```

```rust
use adder;

mod common;

#[test]
fn test_something() {
	common::helper_fn();
	// snip..
}
```

## Controlling The Tests
When using `cargo test`, We can pass number of command line arguments. Some of this arguements are for `cargo test` command itself, Other's are for the test binary that `cargo test` command builds. To separate these two types of arguments, we list the arguments that go to `cargo test` followed by the separator `--` and then the ones that go to the test binary.

- **Tell Compiler to use limited number of threads to run test functions:** `cargo test -- --test-threads=2`
- **Show the output of passed tests:** `cargo test -- --show-output` (when we run `cargo test` rust compiler only shows the stdout outputs of failed tests. in-order to enable stdout outputs to passed tests as well, we can use this cmd argument)
- **Filtering to run single test:** `cargo test add_num`
- **Filtaring to run multiple tests:** `cargo test add` (this will run all the tests that has `add` in their name)
- **Run tests in a particular integration test file:** `cargo test --test [integration-test-file-name]`
- **Run ignored tests:** `cargo test -- --ignored`

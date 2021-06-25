 
# Iterators

 - Iterators allow performing a task on series of elements (Vectors, Hashmap, Etc...)
 - In Rust, iterators are **lazy** (they have no effect until calling methods that consume the iterator to use it up)

```rust
let vector = vec![10, 20 30];
let vec_iter = vector.iter(); // just initializing a iterator. not using it. 

// here for loop is consuming the iterator by going through each value;
for num in vec_iter {
	println!("{}", num);	
}
```

All iterators implement a trait named `Iterator`.

*Example Definition of the Iterator trait*
```rust
pub trait Iterator {
	type Item; // assosiated type, the return type of next() method. 
	
	fn next(&mut self) -> Option<Self::Item>;
}
```

`next()` method can be called on an iterator directly. To do this iterator has to be mutable as `next()` takes a mutable reference and update the inner state which tracks where the current pointer is at in the iterator.  `for` loops always takes ownership of the iterator and make it mutable behind the scene. 

```rust
let vector = vec![10, 20, 30];
let mut vec_iter = vector.iter();

assert_eq(vec_iter.next(), Some(&10)); // PASS
assert_eq(vec_iter.next(), Some(&20)); // PASS
assert_eq(vec_iter.next(), Some(30)); // FAIL
assert_eq(vec_iter.next(), None); // PASS
```
`next()` method always returns an immutable reference to the current value. Because iterator doesn't take ownership of the sequence data type. To take ownership of the data type we can use `into_iter()` method instead of `iter()`. Similarly, to get a mutable reference, we can use `iter_mut()` method. 

### Methods that consume the iterator
`Iterator` trait has pre-defined methods that consume the iterator. These methods take the ownership of iterator and call the `next()` method on it. Thus use up the iterator. These methods are called **consuming adaptors**. Once a consuming adapter is used on an iterator, that iterator cannot be used again.  _Eg: `sum()` method_

```rust
let nums = vec![10, 20, 30, 40];
let nums_iter = nums.iter();
let sum: u32 = nums_iter.sum();

println!("Sum is: {}", sum);

// This gives an error as for loop cannot take ownership of iterator
// because sum() consuming adaptor has already took it. 
for item in nums_iter { 
	println!("Num: {}", item);
}
```

### Methods that produce iterators
These methods are also defined in the `Iterator` trait and allow to modify of the current iterator to another type of iterator. These methods are called **Iterator Adapters** and We can chain multiple calls of these methods to perform complex tasks in a readable way. 

```rust 
// Print the even numbers of square roots of 1 - 10
let nums = (1..10).map(| num | num * num).filter(| x | x % 2 == 0); // at this point, iterator is unused. 
for num in nums {
	println!("nums: {}", num);
}
```

## Creating custom iterator types
We can create custom iterator types by implementing the `Iterator` trait into a struct. 

```rust
struct OneToTen {
    cur: u32,
}

impl OneToTen {
    fn new() -> OneToTen {
        OneToTen{cur: 1};
    }
}

impl Iterator for OneToTen {
    type Item = u32;
    
    fn next(&mut self) -> Option<Self::Item> {
        if(self.cur < 11) {
            self.cur += 1;
            Some(self.cur)
        } else {
            None
        }
    }
}
```

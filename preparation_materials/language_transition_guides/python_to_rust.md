# Python to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust for Python Programmers](https://github.com/rochacbruno/py2rs) - Comprehensive comparison (free)
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [PyO3](https://github.com/PyO3/pyo3) - Rust bindings for Python (free)
- [Maturin](https://github.com/PyO3/maturin) - Build and publish Rust-based Python packages (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)

---

## Introduction

This guide is designed specifically for Python developers transitioning to Rust. While both languages aim to be productive and expressive, they take different approaches to memory management, type systems, and concurrency. This guide will help you leverage your Python knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Python and Rust:

### Hello World

**Python:**
```python
print("Hello, World!")
```
*A simple print statement that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust requires a main function as the entry point and uses the println! macro for output.*

### Variables

**Python:**
```python
# Variables are dynamically typed
x = 5
x = "hello"  # Valid in Python - type can change
```
*Python variables can change types at runtime and are dynamically typed.*

**Rust:**
```rust
// Variables are immutable by default
let x = 5;
// x = 10; // This would cause an error

// To make a variable mutable:
let mut y = 5;
y = 10; // This is valid

// Type annotations are optional but can be explicit
let z: i32 = 42;
```
*Rust variables are statically typed and immutable by default, requiring the mut keyword for mutability.*

### Functions

**Python:**
```python
def add(a, b):
    return a + b

# Type hints (optional)
def add_typed(a: int, b: int) -> int:
    return a + b
```
*Python functions use the def keyword, with optional type hints for better IDE support.*

**Rust:**
```rust
fn add(a: i32, b: i32) -> i32 {
    a + b  // Note: no return keyword needed for the last expression
}

// With explicit return
fn add_explicit(a: i32, b: i32) -> i32 {
    return a + b;
}
```
*Rust functions require explicit type annotations for parameters and return values, and the last expression is implicitly returned if there's no semicolon.*

## Basic Types

### Numbers

**Python:**
```python
# Integers (arbitrary precision)
x = 42
big_num = 10**100

# Floating point
y = 3.14
```
*Python integers have arbitrary precision, automatically expanding as needed.*

**Rust:**
```rust
// Integers with specific bit sizes
let x: i32 = 42;  // 32-bit signed integer
let y: u64 = 100;  // 64-bit unsigned integer

// Floating point
let z: f64 = 3.14;  // 64-bit float
```
*Rust has specific integer types with defined sizes, requiring explicit handling of potential overflow.*

### Strings

**Python:**
```python
# Strings are immutable sequences of Unicode characters
s1 = "Hello"
s2 = 'World'
s3 = f"{s1}, {s2}!"  # f-strings for interpolation
```
*Python strings are immutable Unicode sequences with easy interpolation via f-strings.*

**Rust:**
```rust
// String literals (str) vs owned Strings
let s1 = "Hello";  // &str - string slice, immutable reference
let s2 = String::from("World");  // String - owned, growable
let s3 = format!("{}, {}!", s1, s2);  // format! macro for interpolation
```
*Rust distinguishes between string slices (&str) and owned Strings, with different memory characteristics.*

### Collections

**Python:**
```python
# Lists
my_list = [1, 2, 3]
my_list.append(4)

# Dictionaries
my_dict = {"key": "value"}
my_dict["new_key"] = "new_value"

# Sets
my_set = {1, 2, 3}
```
*Python has built-in dynamic collections like lists, dictionaries, and sets.*

**Rust:**
```rust
// Vectors (similar to Python lists)
let mut my_vec = vec![1, 2, 3];
my_vec.push(4);

// HashMaps (similar to Python dictionaries)
use std::collections::HashMap;
let mut my_map = HashMap::new();
my_map.insert("key", "value");
my_map.insert("new_key", "new_value");

// HashSets (similar to Python sets)
use std::collections::HashSet;
let mut my_set = HashSet::new();
my_set.insert(1);
my_set.insert(2);
```
*Rust collections are more explicit, with Vec for dynamic arrays, HashMap for key-value stores, and HashSet for unique values.*

## Control Flow

### Conditionals

**Python:**
```python
if x > 5:
    print("x is greater than 5")
elif x == 5:
    print("x is equal to 5")
else:
    print("x is less than 5")
```
*Python uses indentation to define code blocks in conditionals.*

**Rust:**
```rust
if x > 5 {
    println!("x is greater than 5");
} else if x == 5 {
    println!("x is equal to 5");
} else {
    println!("x is less than 5");
}

// If can be an expression
let message = if x > 5 { "greater" } else { "less or equal" };
```
*Rust uses braces for code blocks and allows if expressions to return values.*

### Loops

**Python:**
```python
# For loop over range
for i in range(5):
    print(i)

# For loop over collection
for item in my_list:
    print(item)

# While loop
while condition:
    # do something
    if should_break:
        break
```
*Python loops iterate over iterables like ranges or collections.*

**Rust:**
```rust
// For loop over range
for i in 0..5 {
    println!("{}", i);
}

// For loop over collection
for item in &my_vec {
    println!("{}", item);
}

// While loop
while condition {
    // do something
    if should_break {
        break;
    }
}

// Infinite loop
loop {
    // do something
    if should_exit {
        break;
    }
}
```
*Rust has similar loop constructs but with different syntax and an additional loop keyword for infinite loops.*

## Error Handling Basics

### Python vs Rust Approach

**Python:**
```python
# Exception-based error handling
try:
    result = risky_operation()
except SomeError as e:
    print(f"Error occurred: {e}")
    # handle error
else:
    # code that runs if no exception
finally:
    # cleanup code that always runs
```
*Python uses exceptions for error handling with try/except blocks.*

**Rust:**
```rust
// Result-based error handling
match risky_operation() {
    Ok(result) => {
        // handle successful result
    },
    Err(e) => {
        println!("Error occurred: {}", e);
        // handle error
    }
}

// Or using the ? operator for early returns
fn process() -> Result<(), MyError> {
    let result = risky_operation()?;  // Returns from function if Err
    // Continue processing with result
    Ok(())
}
```
*Rust uses the Result enum for error handling, with explicit handling via match or the ? operator.*

## Ownership and Borrowing

This is one of the most significant differences between Python and Rust:

**Python:**
```python
# Python uses reference counting and garbage collection
def process(data):
    # Both functions have access to the same data
    transform(data)
    analyze(data)
```
*Python manages memory automatically through reference counting and garbage collection.*

**Rust:**
```rust
fn process(data: Vec<i32>) {
    // This transfers ownership of data to transform
    transform(data);
    
    // This would cause an error - data has been moved
    // analyze(data);
}

// Instead, we can:

// 1. Borrow immutably
fn process_borrow(data: &Vec<i32>) {
    // Both functions borrow data immutably
    transform(&data);
    analyze(&data);
}

// 2. Borrow mutably (but only one at a time)
fn process_mut_borrow(data: &mut Vec<i32>) {
    // Transform borrows data mutably
    transform_mut(data);
    
    // Now analyze can borrow it
    analyze(data);
}

// 3. Clone if needed
fn process_clone(data: Vec<i32>) {
    // Create a copy of data for transform
    transform(data.clone());
    
    // Original data still available
    analyze(data);
}
```
*Rust's ownership system ensures memory safety without garbage collection, using borrowing rules to prevent data races and use-after-free bugs.*

## Structs and Enums

### Classes vs Structs

**Python:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def greet(self):
        return f"Hello, my name is {self.name}"

# Usage
person = Person("Alice", 30)
print(person.greet())
```
*Python uses classes for object-oriented programming with methods defined inside the class.*

**Rust:**
```rust
struct Person {
    name: String,
    age: u32,
}

impl Person {
    // Constructor-like function
    fn new(name: String, age: u32) -> Person {
        Person { name, age }
    }
    
    // Method
    fn greet(&self) -> String {
        format!("Hello, my name is {}", self.name)
    }
}

// Usage
let person = Person::new(String::from("Alice"), 30);
println!("{}", person.greet());
```
*Rust separates data (struct) from behavior (impl), with explicit self references in methods.*

### Enums

**Python:**
```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3
```
*Python enums are simple named constants.*

**Rust:**
```rust
enum Color {
    Red,
    Green,
    Blue,
}

// Enums can contain data
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```
*Rust enums are more powerful, allowing associated data of different types with each variant.*

## Conclusion

This guide covers the fundamental differences between Python and Rust to help you transition between the languages. Remember that Rust's strict compile-time checks may feel restrictive at first, but they prevent entire classes of bugs that would only be caught at runtime in Python.

As you continue your Rust journey, focus on understanding ownership, borrowing, and lifetimes—these concepts are central to Rust's memory safety guarantees and are the most significant departure from Python's approach.

## Next Steps

- Work through the [Rustlings](https://github.com/rust-lang/rustlings) exercises
- Build a small project in Rust that's similar to something you've built in Python
- Explore the [PyO3](https://github.com/PyO3/pyo3) library if you need to integrate Rust with existing Python code
- Join the [Rust Discord](https://discord.gg/rust-lang) or [Rust Users Forum](https://users.rust-lang.org/) to get help from the community

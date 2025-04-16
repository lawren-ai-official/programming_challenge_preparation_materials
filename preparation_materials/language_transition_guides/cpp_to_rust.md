# C++ to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust for C++ Programmers](https://github.com/nrc/r4cppp) - Comprehensive comparison (free)
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [cxx](https://github.com/dtolnay/cxx) - Safe FFI between Rust and C++ (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [The Rust Performance Book](https://nnethercote.github.io/perf-book/) - Performance optimization guide (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For C++ developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - C++ vs Rust syntax
2. [Basic Types](#basic-types) - Understanding Rust's type system
3. [Control Flow](#control-flow) - Loops and conditionals in Rust
4. [Functions](#functions) - Defining and calling functions
5. [Error Handling Basics](#error-handling-basics) - Result and Option types

### 🔍 Standard Track (1-2 days)
For a balanced learning experience:
1. Complete the Fast Track
2. [Ownership and Borrowing](#ownership-and-borrowing) - Rust's memory management
3. [Structs and Enums](#structs-and-enums) - Custom data types
4. [Collections](#collections) - Vectors, HashMaps, and more
5. [Pattern Matching](#pattern-matching) - Powerful pattern matching
6. [Modules and Packages](#modules-and-packages) - Code organization

### 🧠 Comprehensive Track (3-5 days)
For a deep understanding of Rust from a C++ perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Unsafe Rust](#unsafe-rust) - Low-level control when needed
5. [FFI](#ffi) - Interoperating with C and C++
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for C++ developers transitioning to Rust. While both languages aim for systems-level performance and control, they take different approaches to memory management, error handling, and concurrency. This guide will help you leverage your C++ knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between C++ and Rust:

### Hello World

**C++:**
```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```
*A simple C++ program that outputs text to the console using the iostream library.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro and implicit return.*

### Variables and Types

**C++:**
```cpp
#include <string>

int main() {
    // Variables are mutable by default
    int x = 5;
    x = 10; // Valid
    
    // Constants
    const int y = 42;
    // y = 43; // Error: assignment of read-only variable
    
    // Type inference with auto
    auto z = 3.14;
    
    // String handling
    std::string message = "Hello";
    message += ", World!";
}
```
*C++ variables are mutable by default, with const for immutability. Type inference is available with auto.*

**Rust:**
```rust
fn main() {
    // Variables are immutable by default
    let x = 5;
    // x = 10; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut y = 5;
    y = 10; // Valid
    
    // Constants
    const Z: i32 = 42;
    
    // Type inference
    let pi = 3.14; // f64 by default
    
    // String handling
    let mut message = String::from("Hello");
    message.push_str(", World!");
}
```
*Rust variables are immutable by default, requiring mut for mutability. Type inference is built-in, and strings are handled differently.*

### Functions

**C++:**
```cpp
#include <string>
#include <vector>

// Function with return value
int add(int a, int b) {
    return a + b;
}

// Function with reference parameters
void modify(std::vector<int>& vec) {
    vec.push_back(42);
}

// Function with const reference (read-only)
int sum(const std::vector<int>& vec) {
    int total = 0;
    for (const auto& val : vec) {
        total += val;
    }
    return total;
}

// Function with move semantics
std::string concatenate(std::string&& a, std::string&& b) {
    return std::move(a) + std::move(b);
}
```
*C++ functions with various parameter passing styles: by value, by reference, by const reference, and with move semantics.*

**Rust:**
```rust
// Function with return value
fn add(a: i32, b: i32) -> i32 {
    a + b // No semicolon means this is the return value
}

// Function that modifies a parameter
fn modify(vec: &mut Vec<i32>) {
    vec.push(42);
}

// Function with read-only reference
fn sum(vec: &Vec<i32>) -> i32 {
    let mut total = 0;
    for val in vec {
        total += val;
    }
    total
}

// Function that takes ownership
fn concatenate(mut a: String, b: String) -> String {
    a.push_str(&b);
    a
}
```
*Rust functions with various parameter passing styles: by value (taking ownership), by mutable reference, and by immutable reference.*

## Memory Management

This is one of the most significant differences between C++ and Rust:

### RAII vs Ownership

**C++:**
```cpp
#include <memory>
#include <vector>

void process_data() {
    // Raw pointer (manual memory management)
    int* raw_ptr = new int(42);
    // ... use raw_ptr
    delete raw_ptr; // Must remember to free
    
    // RAII with unique_ptr (automatic cleanup)
    auto smart_ptr = std::make_unique<int>(42);
    // ... use smart_ptr
    // Automatically freed when out of scope
    
    // Shared ownership
    auto shared = std::make_shared<std::vector<int>>();
    shared->push_back(1);
    
    // Another reference to the same data
    auto shared2 = shared;
    shared2->push_back(2);
    
    // Both shared and shared2 point to the same vector
    // Memory freed when all shared_ptrs are gone
}
```
*C++ uses RAII (Resource Acquisition Is Initialization) with smart pointers for automatic resource management, but still allows manual memory management with raw pointers.*

**Rust:**
```rust
fn process_data() {
    // Ownership model
    let s = String::from("hello");
    // s is owner of the string data
    
    // This moves ownership to the process function
    process(s);
    
    // s is no longer valid here
    // println!("{}", s); // Error: use of moved value
    
    // Box for heap allocation (similar to unique_ptr)
    let boxed = Box::new(42);
    // Automatically freed when out of scope
    
    // Shared ownership with reference counting
    let shared = Rc::new(vec![1, 2, 3]);
    
    // Clone the Rc to create another reference
    let shared2 = shared.clone();
    
    // Both shared and shared2 point to the same vector
    // Memory freed when all Rc references are gone
}

fn process(s: String) {
    // s is owned by this function now
    println!("{}", s);
    // s is dropped (freed) when this function ends
}
```
*Rust's ownership system enforces memory safety at compile time. Resources have exactly one owner, and ownership can be transferred (moved) or temporarily borrowed.*

### Borrowing vs References

**C++:**
```cpp
#include <vector>
#include <iostream>

void print_vector(const std::vector<int>& vec) {
    for (const auto& val : vec) {
        std::cout << val << " ";
    }
    std::cout << std::endl;
}

void modify_vector(std::vector<int>& vec) {
    vec.push_back(42);
}

int main() {
    std::vector<int> numbers = {1, 2, 3};
    
    // Pass by const reference (read-only)
    print_vector(numbers);
    
    // Pass by reference (can modify)
    modify_vector(numbers);
    
    // Multiple references are allowed
    auto& ref1 = numbers;
    auto& ref2 = numbers;
    
    // Can have both mutable references simultaneously
    ref1.push_back(10);
    ref2.push_back(20);
}
```
*C++ allows multiple mutable references to the same data, which can lead to data races in concurrent code.*

**Rust:**
```rust
fn print_vector(vec: &Vec<i32>) {
    for val in vec {
        print!("{} ", val);
    }
    println!();
}

fn modify_vector(vec: &mut Vec<i32>) {
    vec.push(42);
}

fn main() {
    let mut numbers = vec![1, 2, 3];
    
    // Immutable borrow (read-only)
    print_vector(&numbers);
    
    // Mutable borrow (can modify)
    modify_vector(&mut numbers);
    
    // Multiple immutable borrows are allowed
    let ref1 = &numbers;
    let ref2 = &numbers;
    
    // Can use both immutable references
    println!("{:?} {:?}", ref1, ref2);
    
    // But can't have mutable and immutable borrows simultaneously
    // let ref_mut = &mut numbers; // Error if ref1 and ref2 are still in use
    
    // After the last use of ref1 and ref2, we can create a mutable borrow
    let ref_mut = &mut numbers;
    ref_mut.push(10);
    
    // Can't use ref1 or create a new immutable borrow while ref_mut is active
    // println!("{:?}", ref1); // Error
}
```
*Rust's borrowing rules prevent data races at compile time: either multiple immutable borrows OR exactly one mutable borrow, but not both simultaneously.*

## Templates vs Generics

### Basic Parameterization

**C++:**
```cpp
#include <vector>
#include <string>

// Function template
template<typename T>
T max(T a, T b) {
    return a > b ? a : b;
}

// Class template
template<typename T>
class Stack {
private:
    std::vector<T> elements;
    
public:
    void push(T value) {
        elements.push_back(value);
    }
    
    T pop() {
        T top = elements.back();
        elements.pop_back();
        return top;
    }
    
    bool empty() const {
        return elements.empty();
    }
};

int main() {
    // Using function template
    int max_int = max<int>(10, 20);
    double max_double = max(3.14, 2.71); // Type inference
    
    // Using class template
    Stack<int> int_stack;
    int_stack.push(42);
    
    Stack<std::string> string_stack;
    string_stack.push("Hello");
}
```
*C++ templates are a compile-time mechanism for generating code for different types. Type checking happens after template instantiation.*

**Rust:**
```rust
// Function with generics
fn max<T: std::cmp::PartialOrd>(a: T, b: T) -> T {
    if a > b { a } else { b }
}

// Struct with generics
struct Stack<T> {
    elements: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self {
        Stack { elements: Vec::new() }
    }
    
    fn push(&mut self, value: T) {
        self.elements.push(value);
    }
    
    fn pop(&mut self) -> Option<T> {
        self.elements.pop()
    }
    
    fn is_empty(&self) -> bool {
        self.elements.is_empty()
    }
}

fn main() {
    // Using generic function
    let max_int = max(10, 20);
    let max_float = max(3.14, 2.71);
    
    // Using generic struct
    let mut int_stack = Stack::new();
    int_stack.push(42);
    
    let mut string_stack = Stack::<String>::new();
    string_stack.push(String::from("Hello"));
}
```
*Rust generics are similar to C++ templates but with earlier type checking. Constraints on generic types are specified using traits.*

### Constraints

**C++:**
```cpp
#include <iostream>
#include <type_traits>

// C++20 concepts
template<typename T>
concept Numeric = std::is_arithmetic_v<T>;

// Function with concept constraint
template<Numeric T>
T add(T a, T b) {
    return a + b;
}

// Pre-C++20 SFINAE approach
template<typename T, 
         typename = typename std::enable_if<std::is_arithmetic<T>::value>::type>
T multiply(T a, T b) {
    return a * b;
}

int main() {
    // Works with numeric types
    std::cout << add(5, 10) << std::endl;
    std::cout << add(3.14, 2.71) << std::endl;
    
    // Error: std::string is not a Numeric type
    // std::cout << add(std::string("Hello"), std::string("World")) << std::endl;
}
```
*C++ uses concepts (C++20) or SFINAE (Substitution Failure Is Not An Error) for constraining templates.*

**Rust:**
```rust
use std::ops::Add;

// Function with trait bound
fn add<T: Add<Output = T>>(a: T, b: T) -> T {
    a + b
}

// Alternative syntax with where clause
fn multiply<T>(a: T, b: T) -> T 
where 
    T: std::ops::Mul<Output = T>,
{
    a * b
}

fn main() {
    // Works with types that implement Add
    println!("{}", add(5, 10));
    println!("{}", add(3.14, 2.71));
    
    // Error: &str doesn't implement Add
    // println!("{}", add("Hello", "World"));
    
    // But String + &str works because String implements Add<&str>
    println!("{}", String::from("Hello") + "World");
}
```
*Rust uses trait bounds to constrain generic types, ensuring they implement required functionality.*

## Error Handling

### Exceptions vs Results

**C++:**
```cpp
#include <iostream>
#include <stdexcept>
#include <fstream>
#include <string>

// Function that throws exceptions
std::string read_file(const std::string& filename) {
    std::ifstream file(filename);
    if (!file.is_open()) {
        throw std::runtime_error("Failed to open file: " + filename);
    }
    
    std::string content;
    std::string line;
    while (std::getline(file, line)) {
        content += line + "\n";
    }
    
    return content;
}

int main() {
    try {
        std::string content = read_file("example.txt");
        std::cout << "File content: " << content << std::endl;
    } catch (const std::exception& e) {
        std::cerr << "Error: " << e.what() << std::endl;
        return 1;
    }
    
    return 0;
}
```
*C++ uses exceptions for error handling, which are propagated up the call stack until caught.*

**Rust:**
```rust
use std::fs::File;
use std::io::{self, Read};
use std::path::Path;

// Function that returns a Result
fn read_file(filename: &str) -> Result<String, io::Error> {
    let mut file = File::open(filename)?;
    let mut content = String::new();
    file.read_to_string(&mut content)?;
    Ok(content)
}

fn main() -> Result<(), io::Error> {
    // Using the ? operator to propagate errors
    let content = read_file("example.txt")?;
    println!("File content: {}", content);
    
    // Alternative: match on the Result
    match read_file("another.txt") {
        Ok(content) => println!("Another file: {}", content),
        Err(error) => println!("Error reading another file: {}", error),
    }
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, making errors explicit in function signatures and forcing error handling at compile time.*

### Error Propagation

**C++:**
```cpp
#include <iostream>
#include <stdexcept>
#include <string>

// Functions that propagate exceptions
void process_data() {
    // If validate() or transform() throw, the exception
    // will propagate to the caller
    std::string data = validate();
    std::string result = transform(data);
    store(result);
}

int main() {
    try {
        process_data();
        std::cout << "Processing succeeded" << std::endl;
    } catch (const std::invalid_argument& e) {
        std::cerr << "Validation error: " << e.what() << std::endl;
    } catch (const std::runtime_error& e) {
        std::cerr << "Processing error: " << e.what() << std::endl;
    } catch (...) {
        std::cerr << "Unknown error occurred" << std::endl;
    }
}
```
*C++ exceptions are automatically propagated up the call stack, and can be caught by type.*

**Rust:**
```rust
// Custom error enum
#[derive(Debug)]
enum AppError {
    ValidationError(String),
    TransformError(String),
    StorageError(String),
}

// Functions that return Results
fn validate() -> Result<String, AppError> {
    // Implementation
    Ok(String::from("valid data"))
}

fn transform(data: String) -> Result<String, AppError> {
    // Implementation
    Ok(format!("transformed: {}", data))
}

fn store(result: String) -> Result<(), AppError> {
    // Implementation
   
(Content truncated due to size limit. Use line ranges to read in chunks)
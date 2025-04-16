# Go to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust for Gophers](https://github.com/mre/rust-for-go-developers) - Comparison guide for Go developers (free)
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples (free)
- [Tokio Documentation](https://tokio.rs/) - Async runtime for Rust (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Go developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Go vs Rust syntax
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
For a deep understanding of Rust from a Go perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [Go Interoperability](#go-interoperability) - Using Rust with Go
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Go developers transitioning to Rust. While both languages aim to be productive and expressive, they take different approaches to memory management, type systems, and concurrency. This guide will help you leverage your Go knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Go and Rust:

### Hello World

**Go:**
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```
*A simple Go program that imports the fmt package and prints to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring imports for basic functionality.*

### Variables and Types

**Go:**
```go
package main

import "fmt"

func main() {
    // Variable declaration with type inference
    message := "Hello"
    
    // Explicit type declaration
    var count int = 42
    
    // Zero values
    var defaultInt int       // 0
    var defaultString string // ""
    var defaultBool bool     // false
    
    // Constants
    const pi = 3.14159
    
    // Multiple assignment
    a, b := 1, 2
    
    fmt.Println(message, count, a, b)
}
```
*Go uses := for declaration with type inference and var for explicit declarations. Variables have zero values by default.*

**Rust:**
```rust
fn main() {
    // Variable declaration (immutable by default)
    let message = "Hello";
    
    // Explicit type declaration
    let count: i32 = 42;
    
    // Mutable variables
    let mut counter = 0;
    counter += 1;
    
    // No implicit zero values - variables must be initialized
    // let uninitialized: i32; // Error if used before assignment
    
    // Constants
    const PI: f64 = 3.14159;
    
    // Multiple assignment via tuples
    let (a, b) = (1, 2);
    
    println!("{} {} {} {}", message, count, a, b);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Variables must be initialized before use, and type annotations are optional but can be explicit.*

### Functions

**Go:**
```go
package main

import "fmt"

// Function with parameters and return value
func add(a, b int) int {
    return a + b
}

// Multiple return values
func divAndRemainder(a, b int) (int, int) {
    return a / b, a % b
}

// Named return values
func calculate(a, b int) (sum int, product int) {
    sum = a + b
    product = a * b
    return // Implicit return of named values
}

func main() {
    result := add(5, 3)
    fmt.Println("5 + 3 =", result)
    
    div, rem := divAndRemainder(10, 3)
    fmt.Printf("10 / 3 = %d remainder %d\n", div, rem)
    
    sum, product := calculate(4, 5)
    fmt.Printf("4 + 5 = %d, 4 * 5 = %d\n", sum, product)
}
```
*Go functions can return multiple values and use named return values.*

**Rust:**
```rust
// Function with parameters and return value
fn add(a: i32, b: i32) -> i32 {
    a + b // No semicolon means this is the return value
}

// Multiple return values via tuples
fn div_and_remainder(a: i32, b: i32) -> (i32, i32) {
    (a / b, a % b)
}

// Using a struct for named returns
struct CalcResult {
    sum: i32,
    product: i32,
}

fn calculate(a: i32, b: i32) -> CalcResult {
    CalcResult {
        sum: a + b,
        product: a * b,
    }
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let (div, rem) = div_and_remainder(10, 3);
    println!("10 / 3 = {} remainder {}", div, rem);
    
    let calc = calculate(4, 5);
    println!("4 + 5 = {}, 4 * 5 = {}", calc.sum, calc.product);
}
```
*Rust functions return a single value, but can use tuples or structs for multiple return values. The last expression without a semicolon is implicitly returned.*

## Error Handling

This is one of the significant differences between Go and Rust:

### Error Return Values vs Result

**Go:**
```go
package main

import (
    "errors"
    "fmt"
    "os"
)

// Function that returns an error
func openFile(path string) (*os.File, error) {
    file, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    return file, nil
}

// Custom error
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

func main() {
    // Error handling with if err != nil
    file, err := openFile("example.txt")
    if err != nil {
        fmt.Println("Error opening file:", err)
        return
    }
    defer file.Close()
    
    // Another example
    result, err := divide(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("Result:", result)
}
```
*Go uses multiple return values for error handling, typically checking if err != nil after function calls.*

**Rust:**
```rust
use std::fs::File;
use std::io::{self, Read};
use std::path::Path;

// Function that returns a Result
fn open_file(path: &str) -> Result<File, io::Error> {
    File::open(path)
}

// Custom error with thiserror
use thiserror::Error;

#[derive(Error, Debug)]
enum MathError {
    #[error("division by zero")]
    DivisionByZero,
}

fn divide(a: f64, b: f64) -> Result<f64, MathError> {
    if b == 0.0 {
        return Err(MathError::DivisionByZero);
    }
    Ok(a / b)
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Error handling with ? operator
    let mut file = open_file("example.txt")?;
    
    // Alternative: match on the Result
    let result = match divide(10.0, 0.0) {
        Ok(value) => value,
        Err(e) => {
            println!("Error: {}", e);
            return Ok(());
        }
    };
    
    println!("Result: {}", result);
    Ok(())
}
```
*Rust uses the Result enum for error handling, with the ? operator for propagation and match for explicit handling.*

### Error Propagation

**Go:**
```go
package main

import (
    "errors"
    "fmt"
)

// Functions that propagate errors
func readConfig(path string) ([]byte, error) {
    // ...
    return nil, errors.New("config file not found")
}

func parseConfig(data []byte) (Config, error) {
    // ...
    return Config{}, errors.New("invalid config format")
}

func setupApp() error {
    // Manual error propagation
    data, err := readConfig("config.json")
    if err != nil {
        return fmt.Errorf("setup failed: %w", err)
    }
    
    config, err := parseConfig(data)
    if err != nil {
        return fmt.Errorf("setup failed: %w", err)
    }
    
    // Use config...
    return nil
}

func main() {
    if err := setupApp(); err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("App started successfully")
}
```
*Go requires manual error checking and propagation with if err != nil. Go 1.13+ supports error wrapping with %w.*

**Rust:**
```rust
use std::fs;
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("config file error: {0}")]
    ConfigFileError(String),
    
    #[error("config parse error: {0}")]
    ConfigParseError(String),
}

// Functions that return Results
fn read_config(path: &str) -> Result<Vec<u8>, AppError> {
    // ...
    Err(AppError::ConfigFileError("config file not found".to_string()))
}

fn parse_config(data: &[u8]) -> Result<Config, AppError> {
    // ...
    Err(AppError::ConfigParseError("invalid config format".to_string()))
}

fn setup_app() -> Result<(), AppError> {
    // Automatic error propagation with ?
    let data = read_config("config.json")?;
    let config = parse_config(&data)?;
    
    // Use config...
    Ok(())
}

fn main() {
    match setup_app() {
        Ok(()) => println!("App started successfully"),
        Err(e) => println!("Error: {}", e),
    }
}
```
*Rust uses the ? operator for concise error propagation, with custom error types for type-safe error handling.*

## Concurrency Models

### Goroutines vs Async/Await

**Go:**
```go
package main

import (
    "fmt"
    "net/http"
    "sync"
    "time"
)

func fetchURL(url string, wg *sync.WaitGroup) {
    defer wg.Done()
    
    start := time.Now()
    resp, err := http.Get(url)
    if err != nil {
        fmt.Printf("Error fetching %s: %v\n", url, err)
        return
    }
    defer resp.Body.Close()
    
    fmt.Printf("Fetched %s in %v\n", url, time.Since(start))
}

func main() {
    urls := []string{
        "https://golang.org",
        "https://rust-lang.org",
        "https://github.com",
    }
    
    var wg sync.WaitGroup
    
    for _, url := range urls {
        wg.Add(1)
        // Launch a goroutine
        go fetchURL(url, &wg)
    }
    
    // Wait for all goroutines to complete
    wg.Wait()
    fmt.Println("All URLs fetched")
}
```
*Go uses lightweight goroutines with channels and WaitGroups for concurrency.*

**Rust:**
```rust
use tokio::runtime::Runtime;
use reqwest;
use std::time::Instant;

async fn fetch_url(url: &str) {
    let start = Instant::now();
    
    match reqwest::get(url).await {
        Ok(resp) => {
            println!("Fetched {} in {:?}", url, start.elapsed());
        },
        Err(e) => {
            println!("Error fetching {}: {}", url, e);
        }
    }
}

fn main() {
    let urls = vec![
        "https://www.rust-lang.org",
        "https://golang.org",
        "https://github.com",
    ];
    
    // Create a multi-threaded runtime
    let rt = Runtime::new().unwrap();
    
    rt.block_on(async {
        let mut handles = vec![];
        
        for url in urls {
            // Spawn an async task
            let handle = tokio::spawn(fetch_url(url));
            handles.push(handle);
        }
        
        // Wait for all tasks to complete
        for handle in handles {
            let _ = handle.await;
        }
        
        println!("All URLs fetched");
    });
}
```
*Rust uses async/await with futures and runtimes like Tokio for asynchronous programming.*

### Channels

**Go:**
```go
package main

import (
    "fmt"
    "time"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for job := range jobs {
        fmt.Printf("Worker %d started job %d\n", id, job)
        time.Sleep(time.Second) // Simulate work
        fmt.Printf("Worker %d finished job %d\n", id, job)
        results <- job * 2
    }
}

func main() {
    jobs := make(chan int, 5)
    results := make(chan int, 5)
    
    // Start workers
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }
    
    // Send jobs
    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)
    
    // Collect results
    for a := 1; a <= 5; a++ {
        result := <-results
        fmt.Println("Result:", result)
    }
}
```
*Go uses channels for communication between goroutines, following the "don't communicate by sharing memory; share memory by communicating" principle.*

**Rust:**
```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn worker(id: u32, receiver: mpsc::Receiver<u32>, sender: mpsc::Sender<u32>) {
    for job in receiver {
        println!("Worker {} started job {}", id, job);
        thread::sleep(Duration::from_secs(1)); // Simulate work
        println!("Worker {} finished job {}", id, job);
        sender.send(job * 2).unwrap();
    }
}

fn main() {
    let (job_sender, job_receiver) = mpsc::channel();
    let (result_sender, result_receiver) = mpsc::channel();
    
    // Create shared job receiver
    let job_receiver = std::sync::Arc::new(std::sync::Mutex::new(job_receiver));
    
    // Start workers
    for w in 1..=3 {
        let receiver = job_receiver.clone();
        let sender = result_sender.clone();
        
        thread::spawn(move || {
            let receiver = receiver.lock().unwrap();
            worker(w, receiver.iter(), sender);
        });
    }
    
    // Send jobs
    for j in 1..=5 {
        job_sender.send(j).unwrap();
    }
    drop(job_sender);
    drop(result_sender);
    
    // Collect results
    for result in result_receiver {
        println!("Result: {}", result);
    }
}
```
*Rust uses channels from the standard library for thread communication, with more explicit ownership and thread safety.*

## Memory Management

### Garbage Collection vs Ownership

**Go:**
```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func createPerson(name string, age int) *Person {
    // Allocation on the heap
    p := &Person{
        Name: name,
        Age:  age,
    }
    // Safe to return a pointer to a local variable
    // because Go uses garbage collection
    return p
}

func processPerson(p *Person) {
    fmt.Printf("Processing %s, age %d\n", p.Name, p.Age)
    p.Age++ // Modify the original
}

func main() {
    // Memory is managed by the garbage collector
    alice := createPerson("Alice", 30)
    bob := createPerson("Bob", 25)
    
    processPerson(alice)
    
    fmt.Printf("Alice: %+v\n", alice)
    fmt.Printf("Bob: %+v\n", bob)
    
    // No need to manually free memory
}
```
*Go uses garbage collection to automatically manage memory, allowing functions to return pointers to local variables.*

**Rust:**
```rust
struct Person {
    name: String,
    age: u32,
}

fn create_person(name: &str, age: u32) -> Person {
    // Allocation on the heap (for the String)
    Person {
        name: name.to_string(),
        age,
    }
}

fn process_person(p: &mut Person) {
    println!("Processing {}, age {}", p.name, p.age);
    p.age += 1; // Modify through a mutable reference
}

fn main() {
    // Memory is managed by ownership and borrowing
    let mut alice = create_person("Alice", 30);
    let bob = create_person("Bob", 25);
    
    process_person(&mut alice);
    
    println!("Alice: {:?}", alice);
    println!("Bob: {:?}", bob);
    
    // Memory is automatically freed when variables go out of scope
}

(Content truncated due to size limit. Use line ranges to read in chunks)
# Rust Ramp-Up Guide for Beginners

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [The Rust Programming Language Book](https://doc.rust-lang.org/book/) - The official comprehensive guide (free)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/) - Learn through annotated examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get you used to reading and writing Rust code (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples for various tasks (free)
- [Rust Playground](https://play.rust-lang.org/) - Try Rust code in your browser without installation (free)
- [Crates.io](https://crates.io/) - The Rust package registry (free)
- [This Week in Rust](https://this-week-in-rust.org/) - Stay updated with Rust developments (free)

## Coming from Another Language?

If you're already experienced with another programming language, check out our specialized transition guides:

- [Python to Rust Transition Guide](language_transition_guides/python_to_rust.md)
- [C++ to Rust Transition Guide](language_transition_guides/cpp_to_rust.md)
- [Go to Rust Transition Guide](language_transition_guides/go_to_rust.md)
- [TypeScript to Rust Transition Guide](language_transition_guides/typescript_to_rust.md)
- [Java to Rust Transition Guide](language_transition_guides/java_to_rust.md)
- [C# to Rust Transition Guide](language_transition_guides/csharp_to_rust.md)

These guides provide language-specific comparisons and examples to help you leverage your existing knowledge.

## Related Technology Guides

You'll also want to explore:

- [Leptos Introduction](technology_guides/leptos_introduction.md) - Full-stack web framework for Rust
- [Database Integration Guide](technology_guides/database_integration_guide.md) - Working with PostgreSQL, Redis, and vector databases
- [Cursor AI Guide](technology_guides/cursor_ai_guide.md) - Leveraging AI for Rust development
- [Optional Modules](technology_guides/optional_modules.md) - Additional modules for specific requirements

---

Welcome to the Rust programming language! This guide is designed to help you learn Rust in a practical, step-by-step manner. Rather than overwhelming you with all of Rust's features at once, we'll focus on what you need to know to be productive quickly.

## Why Rust?

Rust offers a unique combination of performance, safety, and developer ergonomics:

- **Performance**: Comparable to C and C++
- **Memory Safety**: Without a garbage collector
- **Thread Safety**: "Fearless concurrency" through the type system
- **Modern Tooling**: Cargo package manager, rustfmt, clippy
- **Helpful Compiler**: Detailed error messages guide you to solutions

## Learning Path

This guide is structured to get you productive as quickly as possible:

1. **Basic Syntax and Concepts**: Get comfortable with Rust's syntax
2. **Building Simple CLI Applications**: Create useful tools quickly
3. **Understanding Ownership**: Rust's unique approach to memory management
4. **Using Common Libraries**: Leverage the ecosystem
5. **Async Programming with Tokio**: Handle concurrent operations
6. **Web Development with Leptos**: Build full-stack applications
7. **Database Integration with Diesel**: Work with PostgreSQL efficiently

## 1. Basic Syntax and Concepts

### Installation

First, install Rust using rustup:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Verify your installation:

```bash
rustc --version
cargo --version
```

### Hello World

Create your first Rust program:

```rust
fn main() {
    println!("Hello, world!");
}
```
*This simple program defines the main entry point function and prints text to the console.*

Save this as `hello.rs` and compile it:

```bash
rustc hello.rs
./hello
```

### Using Cargo

Cargo is Rust's package manager and build system. Create a new project:

```bash
cargo new hello_cargo
cd hello_cargo
```

Build and run your project:

```bash
cargo build   # Compile
cargo run     # Compile and run
cargo check   # Check for errors without producing an executable
```

### Basic Types and Variables

```rust
fn main() {
    // Variables are immutable by default
    let x = 5;
    
    // Make a variable mutable
    let mut y = 10;
    y = 15;
    
    // Basic types
    let integer: i32 = 42;
    let float: f64 = 3.14;
    let boolean: bool = true;
    let character: char = 'A';
    let text: &str = "Hello";
    let string: String = String::from("Hello");
    
    // Tuples
    let tuple: (i32, f64, &str) = (1, 2.5, "tuple");
    let (a, b, c) = tuple;  // Destructuring
    
    // Arrays (fixed size, same type)
    let array: [i32; 5] = [1, 2, 3, 4, 5];
    let first = array[0];
    
    // Vectors (dynamic size, same type)
    let mut vector: Vec<i32> = vec![1, 2, 3];
    vector.push(4);
}
```
*This code demonstrates Rust's variable declaration, mutability, and basic data types including primitives, strings, tuples, arrays, and vectors.*

### Control Flow

```rust
fn main() {
    // If expressions
    let number = 6;
    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else {
        println!("number is not divisible by 4 or 3");
    }
    
    // If as an expression
    let condition = true;
    let number = if condition { 5 } else { 6 };
    
    // Loops
    let mut counter = 0;
    loop {
        counter += 1;
        if counter == 10 {
            break;
        }
    }
    
    // While loops
    let mut number = 3;
    while number != 0 {
        println!("{}!", number);
        number -= 1;
    }
    
    // For loops
    for element in [10, 20, 30].iter() {
        println!("The value is: {}", element);
    }
    
    // Range-based for loops
    for number in 1..4 {  // 1, 2, 3
        println!("{}!", number);
    }
}
```
*This example shows Rust's control flow structures including if/else conditions, loops, while loops, and for loops with iterators and ranges.*

### Functions

```rust
fn main() {
    let result = add(5, 10);
    println!("The sum is: {}", result);
    
    let formatted = format_greeting("Alice");
    println!("{}", formatted);
}

fn add(x: i32, y: i32) -> i32 {
    // The last expression is implicitly returned
    x + y
}

fn format_greeting(name: &str) -> String {
    // Explicit return
    return format!("Hello, {}!", name);
}
```
*This code demonstrates function definitions with parameters, return types, implicit returns (expressions without semicolons), and explicit returns with the return keyword.*

## 2. Building Simple CLI Applications

Let's create a simple command-line application to get comfortable with Rust.

### Project Setup

```bash
cargo new file_counter
cd file_counter
```

Add dependencies to `Cargo.toml`:

```toml
[dependencies]
clap = { version = "4.4", features = ["derive"] }
```

### Implementing a File Counter

Create a simple program that counts files in a directory:

```rust
use std::fs;
use std::path::Path;
use clap::Parser;

/// Simple program to count files in a directory
#[derive(Parser, Debug)]
#[command(author, version, about, long_about = None)]
struct Args {
    /// Directory to count files in
    #[arg(short, long, default_value = ".")]
    directory: String,
    
    /// Whether to count recursively
    #[arg(short, long)]
    recursive: bool,
}

fn main() {
    let args = Args::parse();
    let path = Path::new(&args.directory);
    
    if !path.exists() {
        eprintln!("Error: Directory '{}' does not exist", args.directory);
        return;
    }
    
    if !path.is_dir() {
        eprintln!("Error: '{}' is not a directory", args.directory);
        return;
    }
    
    let count = if args.recursive {
        count_files_recursive(path)
    } else {
        count_files(path)
    };
    
    println!("Found {} files in '{}'", count, args.directory);
}

fn count_files(dir: &Path) -> usize {
    let entries = match fs::read_dir(dir) {
        Ok(entries) => entries,
        Err(e) => {
            eprintln!("Error reading directory: {}", e);
            return 0;
        }
    };
    
    entries.filter_map(Result::ok)
           .filter(|e| e.path().is_file())
           .count()
}

fn count_files_recursive(dir: &Path) -> usize {
    let entries = match fs::read_dir(dir) {
        Ok(entries) => entries,
        Err(e) => {
            eprintln!("Error reading directory: {}", e);
            return 0;
        }
    };
    
    let mut count = 0;
    
    for entry in entries.filter_map(Result::ok) {
        let path = entry.path();
        if path.is_file() {
            count += 1;
        } else if path.is_dir() {
            count += count_files_recursive(&path);
        }
    }
    
    count
}
```
*This CLI application uses clap for argument parsing, defines a recursive file counting function, and demonstrates error handling and filesystem operations in Rust.*

Run your application:

```bash
cargo run -- --directory /path/to/dir --recursive
```

## 3. Understanding Ownership

Ownership is Rust's most unique feature and enables memory safety without garbage collection.

### Key Concepts

- Each value has an "owner"
- There can only be one owner at a time
- When the owner goes out of scope, the value is dropped

```rust
fn main() {
    // String is allocated on the heap
    let s1 = String::from("hello");
    
    // Move: s1 is no longer valid, ownership transferred to s2
    let s2 = s1;
    
    // This would cause a compile error:
    // println!("{}", s1);
    
    // Clone: creates a deep copy
    let s3 = String::from("world");
    let s4 = s3.clone();
    
    // Both s3 and s4 are valid
    println!("s3: {}, s4: {}", s3, s4);
    
    // Primitive types implement Copy trait
    let x = 5;
    let y = x;  // x is still valid because i32 is copied, not moved
    println!("x: {}, y: {}", x, y);
}
```
*This example demonstrates Rust's ownership system, showing how values are moved between variables, how to clone data when needed, and how primitive types are copied automatically.*

### References and Borrowing

References allow you to refer to a value without taking ownership:

```rust
fn main() {
    let s1 = String::from("hello");
    
    // Immutable borrow
    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.", s1, len);
    
    // Mutable borrow
    let mut s2 = String::from("hello");
    change(&mut s2);
    println!("Changed string: {}", s2);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}

fn change(s: &mut String) {
    s.push_str(", world");
}
```
*This code shows how to use references to borrow values without taking ownership, including both immutable references for reading and mutable references for modifying data.*

Borrowing rules:
- You can have either one mutable reference or any number of immutable references
- References must always be valid

### Slices

Slices let you reference a contiguous sequence of elements:

```rust
fn main() {
    let s = String::from("hello world");
    
    let hello = &s[0..5];  // or &s[..5]
    let world = &s[6..11]; // or &s[6..]
    
    println!("{} {}", hello, world);
    
    // String slices
    let first_word = first_word(&s);
    println!("First word: {}", first_word);
}

fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }
    
    &s[..]
}
```
*This example demonstrates string slices, which are references to portions of strings, and shows how to write a function that returns the first word from a string without allocating new memory.*

## 4. Using Common Libraries

Rust has a rich ecosystem of libraries (crates). Here are some common ones:

### Error Handling with `anyhow` and `thiserror`

Add to `Cargo.toml`:

```toml
[dependencies]
anyhow = "1.0"
thiserror = "1.0"
```

Example usage:

```rust
use anyhow::{Context, Result};
use thiserror::Error;
use std::fs;
use std::path::Path;

#[derive(Error, Debug)]
enum AppError {
    #[error("File not found: {0}")]
    FileNotFound(String),
    
    #[error("Permission denied: {0}")]
    PermissionDenied(String),
}

fn read_file(path: &Path) -> Result<String> {
    fs::read_to_string(path)
        .with_context(|| format!("Failed to read file: {}", path.display()))
}

fn process_file(path: &str) -> Result<()> {
    let path = Path::new(path);
    
    if !path.exists() {
        return Err(AppError::FileNotFound(path.to_string_lossy().to_string()).into());
    }
    
    let content = read_file(path)?;
    println!("File content: {}", content);
    
    Ok(())
}

fn main() -> Result<()> {
    process_file("example.txt")?;
    Ok(())
}
```
*This code demonstrates structured error handling in Rust using thiserror for defining custom error types and anyhow for propagating and contextualizing errors throughout your application.*

### Serialization with `serde`

Add to `Cargo.toml`:

```toml
[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

Example usage:

```rust
use serde::{Serialize, Deserialize};
use std::fs;

#[derive(Serialize, Deserialize, Debug)]
struct Person {
    name: String,
    age: u32,
    emails: Vec<String>,
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Create a Person
    let person = Person {
        name: "John Doe".to_string(),
        age: 30,
        emails: vec!["john@example.com".to_string()],
    };
    
    // Serialize to JSON
    let json = serde_json::to_string_pretty(&person)?;
    println!("JSON: {}", json);
    
    // Write to file
    fs::write("person.json", &json)?;
    
    // Read from file
    let json = fs::read_to_string("person.json")?;
    
    // Deserialize from JSON
    let person: Person = serde_json::from_str(&json)?;
    println!("Deserialized person: {:?}", person);
    
    Ok(())
}
```
*This code demonstrates serialization and deserialization of Rust data structures using serde and serde_json.*
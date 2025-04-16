# C# to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [The Rust Programming Language](https://doc.rust-lang.org/book/) - Official Rust book (free)
- [Rust for C# Developers](https://github.com/natemcmaster/rust-for-csharp-developers) - Comparison guide (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For C# developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - C# vs Rust syntax
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
For a deep understanding of Rust from a C# perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [.NET Interoperability](#net-interoperability) - Using Rust with .NET
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for C# developers transitioning to Rust. While both languages are modern and feature-rich, they take different approaches to memory management, type systems, and concurrency. This guide will help you leverage your C# knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between C# and Rust:

### Hello World

**C#:**
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello, World!");
    }
}
```
*A simple C# program with a class containing the Main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**C#:**
```csharp
using System;
using System.Collections.Generic;

class Variables
{
    static void Main()
    {
        // Value types
        int count = 42;
        double pi = 3.14159;
        bool isActive = true;
        char letter = 'A';
        
        // Reference types
        string message = "Hello";
        
        // Arrays and collections
        int[] numbers = { 1, 2, 3, 4, 5 };
        List<string> names = new List<string> { "Alice", "Bob" };
        
        // Constants
        const double Gravity = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var
        var inferred = "Type is inferred";
        
        // Nullable types
        int? nullableInt = null;
        
        Console.WriteLine($"{message} {count}");
    }
}
```
*C# has value and reference types, with explicit type declarations and type inference with var.*

**Rust:**
```rust
fn main() {
    // Basic types
    let count: i32 = 42;
    let pi: f64 = 3.14159;
    let is_active: bool = true;
    let letter: char = 'A';
    
    // String type
    let message: String = String::from("Hello");
    
    // Arrays and vectors
    let numbers: [i32; 5] = [1, 2, 3, 4, 5]; // Fixed-size array
    let names: Vec<String> = vec![
        String::from("Alice"), 
        String::from("Bob")
    ]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    // Optional types (similar to nullable)
    let nullable_int: Option<i32> = None;
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses Option for nullable types.*

### Functions

**C#:**
```csharp
using System;

class Functions
{
    // Method with return value
    static int Add(int a, int b)
    {
        return a + b;
    }
    
    // Method with object parameter
    static void ProcessPerson(Person person)
    {
        Console.WriteLine($"Processing {person.Name}");
        person.Age++; // Modify the object
    }
    
    // Method overloading
    static double Calculate(double a, double b)
    {
        return a + b;
    }
    
    static double Calculate(double a, double b, double c)
    {
        return a + b + c;
    }
    
    // Optional parameters
    static string Greet(string name, string greeting = "Hello")
    {
        return $"{greeting}, {name}!";
    }
    
    // Params array
    static int Sum(params int[] numbers)
    {
        int total = 0;
        foreach (var num in numbers)
        {
            total += num;
        }
        return total;
    }
    
    // Expression-bodied method (C# 6+)
    static double Square(double x) => x * x;
    
    static void Main()
    {
        int result = Add(5, 3);
        Console.WriteLine($"5 + 3 = {result}");
        
        Person john = new Person { Name = "John", Age = 30 };
        ProcessPerson(john);
        Console.WriteLine($"New age: {john.Age}"); // 31
        
        Console.WriteLine($"Sum: {Sum(1, 2, 3, 4, 5)}"); // 15
        Console.WriteLine($"Square: {Square(4)}"); // 16
        Console.WriteLine(Greet("Alice")); // "Hello, Alice!"
        Console.WriteLine(Greet("Bob", "Hi")); // "Hi, Bob!"
    }
}

class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```
*C# functions are methods within classes, supporting overloading, optional parameters, params arrays, and expression-bodied methods.*

**Rust:**
```rust
// Function with return value
fn add(a: i32, b: i32) -> i32 {
    a + b // No semicolon means this is the return value
}

// Struct definition
struct Person {
    name: String,
    age: u32,
}

// Implementation block for methods
impl Person {
    // Constructor-like function
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}

// Function with struct parameter (mutable reference)
fn process_person(person: &mut Person) {
    println!("Processing {}", person.name);
    person.age += 1;
}

// Function with multiple implementations (similar to overloading)
fn calculate(a: f64, b: f64) -> f64 {
    a + b
}

fn calculate_three(a: f64, b: f64, c: f64) -> f64 {
    a + b + c
}

// Function with optional parameter using Option
fn greet(name: &str, greeting: Option<&str>) -> String {
    let greeting = greeting.unwrap_or("Hello");
    format!("{}, {}!", greeting, name)
}

// Function with slice parameter (similar to params)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

// Function expression (similar to expression-bodied method)
fn square(x: f64) -> f64 { x * x }

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
    println!("Square: {}", square(4.0)); // 16
    println!("{}", greet("Alice", None)); // "Hello, Alice!"
    println!("{}", greet("Bob", Some("Hi"))); // "Hi, Bob!"
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**C#:**
```csharp
using System;

// Base class
public class Animal
{
    // Properties
    public string Name { get; set; }
    protected int Age { get; set; }
    
    // Constructor
    public Animal(string name, int age)
    {
        Name = name;
        Age = age;
    }
    
    // Virtual method
    public virtual void MakeSound()
    {
        Console.WriteLine($"{Name} makes a sound");
    }
    
    // Method
    public void Eat()
    {
        Console.WriteLine($"{Name} is eating");
    }
}

// Derived class
public class Dog : Animal
{
    public string Breed { get; private set; }
    
    public Dog(string name, int age, string breed) 
        : base(name, age) // Call base constructor
    {
        Breed = breed;
    }
    
    // Override method
    public override void MakeSound()
    {
        Console.WriteLine($"{Name} barks");
    }
    
    // Additional method
    public void Fetch()
    {
        Console.WriteLine($"{Name} fetches the ball");
    }
}

// Static class
public static class MathUtils
{
    public static double PI = 3.14159;
    
    public static double Square(double x)
    {
        return x * x;
    }
}

// Usage
class Program
{
    static void Main()
    {
        Animal animal = new Animal("Generic Animal", 5);
        animal.MakeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.MakeSound(); // "Rex barks"
        dog.Fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.MakeSound(); // "Buddy barks"
        
        // Static class
        Console.WriteLine(MathUtils.Square(5)); // 25
    }
}
```
*C# uses classes with inheritance for object-oriented programming, with properties, virtual methods, and static classes.*

**Rust:**
```rust
// Struct definition
struct Animal {
    name: String,
    age: u32,
}

// Implementation block for methods
impl Animal {
    // Constructor-like function
    fn new(name: &str, age: u32) -> Self {
        Animal {
            name: name.to_string(),
            age,
        }
    }
    
    // Methods
    fn make_sound(&self) {
        println!("{} makes a sound", self.name);
    }
    
    fn eat(&self) {
        println!("{} is eating", self.name);
    }
}

// No inheritance, but composition
struct Dog {
    animal: Animal,
    breed: String,
}

impl Dog {
    fn new(name: &str, age: u32, breed: &str) -> Self {
        Dog {
            animal: Animal::new(name, age),
            breed: breed.to_string(),
        }
    }
    
    // Method that overrides behavior
    fn make_sound(&self) {
        println!("{} barks", self.animal.name);
    }
    
    // Additional method
    fn fetch(&self) {
        println!("{} fetches the ball", self.animal.name);
    }
}

// Trait for polymorphism
trait MakeSound {
    fn make_sound(&self);
}

impl MakeSound for Animal {
    fn make_sound(&self) {
        println!("{} makes a sound", self.name);
    }
}

impl MakeSound for Dog {
    fn make_sound(&self) {
        println!("{} barks", self.animal.name);
    }
}

// Static functions in a module or impl block
struct MathUtils;

impl MathUtils {
    const PI: f64 = 3.14159;
    
    fn square(x: f64) -> f64 {
        x * x
    }
}

fn main() {
    let animal = Animal::new("Generic Animal", 5);
    animal.make_sound(); // "Generic Animal makes a sound"
    
    let dog = Dog::new("Rex", 3, "German Shepherd");
    dog.make_sound(); // "Rex barks"
    dog.fetch(); // "Rex fetches the ball"
    
    // Polymorphism with trait objects
    let animals: Vec<Box<dyn MakeSound>> = vec![
        Box::new(Animal::new("Generic Animal", 5)),
        Box::new(Dog::new("Buddy", 2, "Labrador")),
    ];
    
    for animal in &animals {
        animal.make_sound();
    }
    
    // Static functions
    println!("{}", MathUtils::square(5.0)); // 25
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**C#:**
```csharp
using System;
using System.IO;

class ErrorHandling
{
    // Method that throws an exception
    static string ReadFile(string path)
    {
        if (!File.Exists(path))
        {
            throw new FileNotFoundException($"File not found: {path}");
        }
        
        return File.ReadAllText(path);
    }
    
    // Custom exception
    class DivisionByZeroException : Exception
    {
        public DivisionByZeroException(string message) : base(message) { }
    }
    
    // Method with custom exception
    static double Divide(double a, double b)
    {
        if (b == 0)
        {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    static void Main()
    {
        // Try-catch for handling exceptions
        try
        {
            string content = ReadFile("example.txt");
            Console.WriteLine($"File content: {content}");
            
            double result = Divide(10, 0);
            Console.WriteLine($"Result: {result}");
        }
        catch (FileNotFoundException e)
        {
            Console.Error.WriteLine($"File error: {e.Message}");
        }
        catch (DivisionByZeroException e)
        {
            Console.Error.WriteLine($"Division error: {e.Message}");
        }
        catch (Exception e)
        {
            Console.Error.WriteLine($"General error: {e.Message}");
        }
        finally
        {
            Console.WriteLine("Cleanup code here");
        }
        
        // Using statement (resource management)
        try
        {
            using (StreamReader reader = new StreamReader("example.txt"))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        catch (Exception e)
        {
            Console.Error.WriteLine($"Error: {e.Message}");
        }
    }
}
```
*C# uses exceptions for error handling with try-catch blocks and using statements for resource management.*

**Rust:**
```rust
use std::fs::File;
use std::io::{self, Read};
use std::path::Path;
use thiserror::Error;

// Function that returns a Result
fn read_file(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?;
    let mut content = String::new();
    file.read_to_string(&mut content)?;
    Ok(content)
}

// Custom error enum
#[derive(Error, Debug)]
enum MathError {
    #[error("division by zero")]
    DivisionByZero,
}

// Function with custom error
fn divide(a: f64, b: f64) -> Result<f64, MathError> {
    if b == 0.0 {
        return Err(MathError::DivisionByZero);
    }
    Ok(a / b)
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Using match for error handling
    match read_file("example.txt") {
        Ok(content) => println!("File content: {}", content),
        Err(e) => println!("File error: {}", e),
    }
    
    // Using ? operator for error propagation
    let content = read_file("example.txt")?;
    println!("File c
(Content truncated due to size limit. Use line ranges to read in chunks)
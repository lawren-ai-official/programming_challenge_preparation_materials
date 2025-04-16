# Java to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [The Rust Programming Language](https://doc.rust-lang.org/book/) - Official Rust book (free)
- [Rust for Java Developers](https://github.com/llogiq/rust-for-java-devs) - Comparison guide (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
        public DivisionByZeroException(String message) {
            super(message);
        }
    }
    
    // Method with custom exception
    public static double divide(double a, double b) throws DivisionByZeroException {
        if (b == 0) {
            throw new DivisionByZeroException("Cannot divide by zero");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        // Try-catch for handling exceptions
        try {
            String content = readFile("example.txt");
            System.out.println("File content: " + content);
            
            double result = divide(10, 0);
            System.out.println("Result: " + result);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (DivisionByZeroException e) {
            System.err.println("Division error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("General error: " + e.getMessage());
        } finally {
            System.out.println("Cleanup code here");
        }
        
        // Try-with-resources (Java 7+)
        try (Scanner scanner = new Scanner(new File("example.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.err.println("File not found");
        }
    }
}
```
*Java uses exceptions for error handling with try-catch blocks and throws declarations.*

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
    println!("File content: {}", content);
    
    // Handling custom errors
    match divide(10.0, 0.0) {
        Ok(result) => println!("Result: {}", result),
        Err(MathError::DivisionByZero) => println!("Division error: cannot divide by zero"),
    }
    
    // Resource management with Drop trait (similar to finally)
    struct CleanupResource;
    
    impl Drop for CleanupResource {
        fn drop(&mut self) {
            println!("Cleanup code here");
        }
    }
    
    let _cleanup = CleanupResource;
    
    Ok(())
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Memory Management

### Garbage Collection vs Ownership

**Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MemoryManagement {
    // Memory is managed by the garbage collector
    public static void main(String[] args) {
        // Object allocation
        Person person = new Person("Alice", 30);
        List<Person> people = new ArrayList<>();
        people.add(person);
        
        // Memory allocation
        int[] numbers = new int[1000];
        
        // Garbage collection
        person = null;
        people.clear();
        numbers = null;
    }
}
```
*Java uses garbage collection for automatic memory management.*

**Rust:**
```rust
fn main() {
    // Ownership and borrowing
    let person = Person::new("Alice", 30);
    let mut people = Vec::new();
    people.push(person);
    
    // Memory allocation
    let numbers = vec![0; 1000];
    
    // Ownership transfer
    let person = None;
    people.clear();
    let numbers = None;
}

struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```
*Rust uses ownership and borrowing for memory management, with explicit memory allocation and deallocation.*

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For Java developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - Java vs Rust syntax
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
For a deep understanding of Rust from a Java perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [JVM Interoperability](#jvm-interoperability) - Using Rust with Java
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for Java developers transitioning to Rust. While both languages support strong typing and have some similar syntax, they take different approaches to memory management, concurrency, and error handling. This guide will help you leverage your Java knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between Java and Rust:

### Hello World

**Java:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
*A simple Java program with a class containing the main method that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust's equivalent is more concise, using the println! macro without requiring a class structure.*

### Variables and Types

**Java:**
```java
public class Variables {
    public static void main(String[] args) {
        // Primitive types
        int count = 42;
        double pi = 3.14159;
        boolean isActive = true;
        char letter = 'A';
        
        // Reference types
        String message = "Hello";
        
        // Arrays
        int[] numbers = {1, 2, 3, 4, 5};
        
        // Constants
        final double GRAVITY = 9.81;
        
        // Variable reassignment
        count = 100; // Valid
        
        // Type inference with var (Java 10+)
        var inferred = "Type is inferred";
        
        System.out.println(message + " " + count);
    }
}
```
*Java has primitive and reference types, with explicit type declarations and limited type inference with var.*

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
    let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector
    
    // Constants
    const GRAVITY: f64 = 9.81;
    
    // Variables are immutable by default
    // count = 100; // Error: cannot assign twice to immutable variable
    
    // Mutable variables
    let mut mutable_count = 42;
    mutable_count = 100; // Valid
    
    // Type inference
    let inferred = "Type is inferred"; // &str
    
    println!("{} {}", message, count);
}
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses String for owned strings.*

### Functions

**Java:**
```java
public class Functions {
    // Method with return value
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Method with object parameter
    public static void processPerson(Person person) {
        System.out.println("Processing " + person.getName());
        person.setAge(person.getAge() + 1);
    }
    
    // Method overloading
    public static double calculate(double a, double b) {
        return a + b;
    }
    
    public static double calculate(double a, double b, double c) {
        return a + b + c;
    }
    
    // Varargs
    public static int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
    
    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);
        
        Person john = new Person("John", 30);
        processPerson(john);
        System.out.println("New age: " + john.getAge()); // 31
        
        System.out.println("Sum: " + sum(1, 2, 3, 4, 5)); // 15
    }
}

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```
*Java functions are methods within classes, supporting overloading, varargs, and object parameters.*

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
    
    // Getter methods
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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

// Function with slice parameter (similar to varargs)
fn sum(numbers: &[i32]) -> i32 {
    let mut total = 0;
    for &num in numbers {
        total += num;
    }
    total
}

fn main() {
    let result = add(5, 3);
    println!("5 + 3 = {}", result);
    
    let mut john = Person::new("John", 30);
    process_person(&mut john);
    println!("New age: {}", john.age); // 31
    
    println!("Sum: {}", sum(&[1, 2, 3, 4, 5])); // 15
}
```
*Rust functions can be standalone or methods in impl blocks. Rust doesn't have method overloading but uses different function names or generic traits.*

## Object-Oriented Programming

### Classes vs Structs and Impls

**Java:**
```java
// Class definition
public class Animal {
    // Instance variables
    private String name;
    protected int age;
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Methods
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// Inheritance
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age); // Call parent constructor
        this.breed = breed;
    }
    
    // Method override
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks");
    }
    
    // Additional method
    public void fetch() {
        System.out.println(getName() + " fetches the ball");
    }
    
    public String getBreed() { return breed; }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Animal animal = new Animal("Generic Animal", 5);
        animal.makeSound(); // "Generic Animal makes a sound"
        
        Dog dog = new Dog("Rex", 3, "German Shepherd");
        dog.makeSound(); // "Rex barks"
        dog.fetch(); // "Rex fetches the ball"
        
        // Polymorphism
        Animal polymorphic = new Dog("Buddy", 2, "Labrador");
        polymorphic.makeSound(); // "Buddy barks"
    }
}
```
*Java uses classes with inheritance for object-oriented programming, with encapsulation through access modifiers.*

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
    
    // Getters (no need for explicit setters with mut)
    fn name(&self) -> &str {
        &self.name
    }
    
    fn age(&self) -> u32 {
        self.age
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
    
    fn breed(&self) -> &str {
        &self.breed
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
}
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance. Traits provide polymorphism.*

## Error Handling

### Exceptions vs Result

**Java:**
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class ErrorHandling {
    // Method that throws an exception
    public static String readFile(String path) throws FileNotFoundException {
        File file = new File(path);
        Scanner scanner = new Scanner(file);
        
        StringBuilder content = new StringBuilder();
        while (scanner.hasNextLine()) {
            content.append(scanner.nextLine()).append("\n");
        }
        
        scanner.close();
        return content.toString();
    }
    
    // Custom exception
    public static class DivisionByZeroException extends Exception {
       
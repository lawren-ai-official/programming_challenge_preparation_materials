# TypeScript to Rust Transition Guide

## Essential Resources & References

Before diving into this guide, here are the key resources you should bookmark:

- [Rust By Example](https://doc.rust-lang.org/rust-by-example/) - Learn through examples (free)
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to get used to reading and writing Rust code (free)
- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/) - Reference for the standard library (free)
- [TypeScript to Rust](https://github.com/typescript-to-rust/typescript-to-rust) - Conversion guide (free)
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Collection of simple examples (free)
- [Wasm Bindgen](https://github.com/rustwasm/wasm-bindgen) - Facilitating high-level interactions between Rust and JavaScript (free)

## Learning Tracks

Choose the track that best fits your needs:

### 🚀 Fast Track (2-3 hours)
For TypeScript developers who need to get productive in Rust quickly:
1. [Syntax Comparison](#syntax-comparison) - TypeScript vs Rust syntax
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
For a deep understanding of Rust from a TypeScript perspective:
1. Complete the Standard Track
2. [Traits and Generics](#traits-and-generics) - Rust's approach to polymorphism
3. [Concurrency](#concurrency) - Safe concurrent programming
4. [Async Programming](#async-programming) - Asynchronous code in Rust
5. [Web Development](#web-development) - Building web applications in Rust
6. [Testing and Documentation](#testing-and-documentation) - Best practices

---

## Introduction

This guide is designed specifically for TypeScript developers transitioning to Rust. While both languages have strong type systems and modern syntax, they take different approaches to memory management, concurrency, and application architecture. This guide will help you leverage your TypeScript knowledge while learning Rust's unique features.

## Syntax Comparison

Let's start by comparing some basic syntax between TypeScript and Rust:

### Hello World

**TypeScript:**
```typescript
console.log("Hello, World!");
```
*A simple TypeScript program that outputs text to the console.*

**Rust:**
```rust
fn main() {
    println!("Hello, World!");
}
```
*Rust requires a main function as the entry point and uses the println! macro for output.*

### Variables and Types

**TypeScript:**
```typescript
// Variable declarations
let message: string = "Hello";
const count: number = 42;

// Type inference
let inferred = "This is a string"; // Type is inferred as string

// Mutable variables
let mutable = 10;
mutable = 20; // Valid

// Arrays
const numbers: number[] = [1, 2, 3, 4, 5];
const mixed: (number | string)[] = [1, "two", 3];

// Objects
interface Person {
    name: string;
    age: number;
}

const person: Person = {
    name: "Alice",
    age: 30
};
```
*TypeScript uses let and const for variable declarations, with optional type annotations and type inference.*

**Rust:**
```rust
// Variable declarations (immutable by default)
let message: String = String::from("Hello");
const COUNT: i32 = 42;

// Type inference
let inferred = "This is a string"; // Type is inferred as &str

// Mutable variables
let mut mutable = 10;
mutable = 20; // Valid

// Arrays and vectors
let numbers: [i32; 5] = [1, 2, 3, 4, 5]; // Fixed-size array
let vector: Vec<i32> = vec![1, 2, 3, 4, 5]; // Dynamic-size vector

// No direct equivalent to union types, but enums can be used
enum Value {
    Number(i32),
    Text(String),
}
let mixed = vec![Value::Number(1), Value::Text(String::from("two")), Value::Number(3)];

// Structs (similar to interfaces)
struct Person {
    name: String,
    age: u32,
}

let person = Person {
    name: String::from("Alice"),
    age: 30,
};
```
*Rust variables are immutable by default, requiring mut for mutability. Rust has more specific numeric types and uses structs instead of objects.*

### Functions

**TypeScript:**
```typescript
// Function with type annotations
function add(a: number, b: number): number {
    return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Optional parameters
function greet(name: string, greeting?: string): string {
    return `${greeting || "Hello"}, ${name}!`;
}

// Default parameters
function createPerson(name: string, age: number = 30): Person {
    return { name, age };
}

// Rest parameters
function sum(...numbers: number[]): number {
    return numbers.reduce((total, n) => total + n, 0);
}

// Function overloading
function process(input: string): string;
function process(input: number): number;
function process(input: string | number): string | number {
    if (typeof input === "string") {
        return input.toUpperCase();
    } else {
        return input * 2;
    }
}
```
*TypeScript functions support type annotations, arrow functions, optional parameters, default values, rest parameters, and function overloading.*

**Rust:**
```rust
// Function with type annotations
fn add(a: i32, b: i32) -> i32 {
    a + b // No semicolon means this is the return value
}

// Closure (similar to arrow function)
let multiply = |a: i32, b: i32| -> i32 { a * b };

// Optional parameters using Option
fn greet(name: &str, greeting: Option<&str>) -> String {
    format!("{}, {}!", greeting.unwrap_or("Hello"), name)
}

// Default parameters using default value in function body
fn create_person(name: String, age: Option<u32>) -> Person {
    Person {
        name,
        age: age.unwrap_or(30),
    }
}

// Variable arguments using slices or vectors
fn sum(numbers: &[i32]) -> i32 {
    numbers.iter().sum()
}

// Function overloading using generics and traits
trait Process {
    type Output;
    fn process(self) -> Self::Output;
}

impl Process for String {
    type Output = String;
    fn process(self) -> String {
        self.to_uppercase()
    }
}

impl Process for i32 {
    type Output = i32;
    fn process(self) -> i32 {
        self * 2
    }
}

fn process<T: Process>(input: T) -> T::Output {
    input.process()
}
```
*Rust functions use fn keyword, explicit return types, and the last expression is implicitly returned if there's no semicolon. Rust uses Option for optional parameters and traits for function overloading.*

## Type Systems

### Static Typing Approaches

**TypeScript:**
```typescript
// Basic types
let name: string = "Alice";
let age: number = 30;
let isActive: boolean = true;

// Union types
let id: string | number = "abc123";
id = 123; // Valid

// Type aliases
type ID = string | number;
let userId: ID = "user123";

// Interfaces
interface User {
    id: ID;
    name: string;
    email: string;
    isAdmin?: boolean; // Optional property
}

// Extending interfaces
interface Employee extends User {
    department: string;
    salary: number;
}

// Generics
function identity<T>(arg: T): T {
    return arg;
}

// Type assertions
const someValue: any = "this is a string";
const strLength: number = (someValue as string).length;

// Nullable types
let nullableString: string | null = "hello";
nullableString = null; // Valid
```
*TypeScript has a structural type system with interfaces, union types, generics, and type assertions.*

**Rust:**
```rust
// Basic types
let name: String = String::from("Alice");
let age: u32 = 30;
let is_active: bool = true;

// Enums (similar to union types)
enum ID {
    Text(String),
    Number(u64),
}

let id = ID::Text(String::from("abc123"));
let id = ID::Number(123); // Valid

// Type aliases
type UserID = String;
let user_id: UserID = String::from("user123");

// Structs (similar to interfaces)
struct User {
    id: String,
    name: String,
    email: String,
    is_admin: Option<bool>, // Optional property using Option
}

// Traits (similar to interfaces for behavior)
trait HasEmail {
    fn get_email(&self) -> &str;
    fn set_email(&mut self, email: String);
}

// Implementing traits
impl HasEmail for User {
    fn get_email(&self) -> &str {
        &self.email
    }
    
    fn set_email(&mut self, email: String) {
        self.email = email;
    }
}

// Generics
fn identity<T>(arg: T) -> T {
    arg
}

// No direct equivalent to type assertions, but can use as for type conversion
let some_value: u32 = 42;
let as_u64: u64 = some_value as u64;

// Option for nullable types
let nullable_string: Option<String> = Some(String::from("hello"));
let nullable_string: Option<String> = None; // Valid
```
*Rust has a nominal type system with structs, enums, traits, and generics. It uses Option for nullable types and has no direct equivalent to type assertions.*

## Error Handling

### Exceptions vs Result

**TypeScript:**
```typescript
// Try-catch for error handling
function divide(a: number, b: number): number {
    if (b === 0) {
        throw new Error("Division by zero");
    }
    return a / b;
}

try {
    const result = divide(10, 0);
    console.log(result);
} catch (error) {
    console.error("Error:", error.message);
}

// Custom error classes
class ValidationError extends Error {
    constructor(message: string) {
        super(message);
        this.name = "ValidationError";
    }
}

// Async error handling
async function fetchData(url: string): Promise<any> {
    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`HTTP error: ${response.status}`);
        }
        return await response.json();
    } catch (error) {
        console.error("Fetch error:", error);
        throw error; // Re-throw
    }
}
```
*TypeScript uses exceptions for error handling with try-catch blocks and custom error classes.*

**Rust:**
```rust
use std::fmt;

// Result for error handling
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        return Err(String::from("Division by zero"));
    }
    Ok(a / b)
}

// Using match to handle Result
match divide(10.0, 0.0) {
    Ok(result) => println!("Result: {}", result),
    Err(error) => println!("Error: {}", error),
}

// Using ? operator for error propagation
fn calculate(a: f64, b: f64) -> Result<f64, String> {
    let div = divide(a, b)?; // Returns early if Err
    Ok(div * 2.0)
}

// Custom error types with thiserror
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("validation error: {0}")]
    ValidationError(String),
    
    #[error("IO error: {0}")]
    IoError(#[from] std::io::Error),
}

// Async error handling
async fn fetch_data(url: &str) -> Result<String, reqwest::Error> {
    let response = reqwest::get(url).await?;
    let body = response.text().await?;
    Ok(body)
}
```
*Rust uses the Result enum for error handling, with match expressions and the ? operator for propagation.*

## Classes vs Structs and Impls

### Object-Oriented Programming

**TypeScript:**
```typescript
// Class definition
class Animal {
    // Properties
    protected name: string;
    
    // Constructor
    constructor(name: string) {
        this.name = name;
    }
    
    // Methods
    speak(): string {
        return `${this.name} makes a noise`;
    }
}

// Inheritance
class Dog extends Animal {
    private breed: string;
    
    constructor(name: string, breed: string) {
        super(name);
        this.breed = breed;
    }
    
    // Method override
    speak(): string {
        return `${this.name} barks`;
    }
    
    // Additional method
    getBreed(): string {
        return this.breed;
    }
}

// Static members
class MathUtils {
    static PI: number = 3.14159;
    
    static square(x: number): number {
        return x * x;
    }
}

// Using classes
const dog = new Dog("Rex", "German Shepherd");
console.log(dog.speak()); // "Rex barks"
console.log(dog.getBreed()); // "German Shepherd"
console.log(MathUtils.square(5)); // 25
```
*TypeScript supports traditional OOP with classes, inheritance, encapsulation, and polymorphism.*

**Rust:**
```rust
// Struct definition
struct Animal {
    name: String,
}

// Implementation block for methods
impl Animal {
    // Constructor-like function
    fn new(name: String) -> Self {
        Animal { name }
    }
    
    // Methods
    fn speak(&self) -> String {
        format!("{} makes a noise", self.name)
    }
}

// No direct inheritance, but composition
struct Dog {
    animal: Animal,
    breed: String,
}

impl Dog {
    fn new(name: String, breed: String) -> Self {
        Dog {
            animal: Animal::new(name),
            breed,
        }
    }
    
    // Method that overrides behavior
    fn speak(&self) -> String {
        format!("{} barks", self.animal.name)
    }
    
    fn get_breed(&self) -> &str {
        &self.breed
    }
}

// Static functions are just regular functions in impl blocks
struct MathUtils;

impl MathUtils {
    const PI: f64 = 3.14159;
    
    fn square(x: f64) -> f64 {
        x * x
    }
}

// Using structs and impls
let dog = Dog::new(String::from("Rex"), String::from("German Shepherd"));
println!("{}", dog.speak()); // "Rex barks"
println!("{}", dog.get_breed()); // "German Shepherd"
println!("{}", MathUtils::square(5.0)); // 25.0
```
*Rust uses structs with implementation blocks instead of classes, and favors composition over inheritance.*

## Interfaces vs Traits

### Polymorphism

**TypeScript:**
```typescript
// Interface definition
interface Shape {
    area(): number;
    perimeter(): number;
}

// Implementing interfaces
class Circle implements Shape {
    constructor(private radius: number) {}
    
    area(): number {
        return Math.PI * this.radius * this.radius;
    }
    
    perimeter(): number {
        return 2 * Math.PI * this.radius;
    }
}

class Rectangle implements Shape {
    constructor(private width: number, private height: number) {}
    
    area(): number {
        return this.width * this.height;
    }
    
    perimeter(): number {
        return 2 * (this.width + this.height);
    }
}

// Function that accepts any Shape
function printShapeInfo(shape: Shape): void {
    console.log(`Area: ${shape.area()}`);
    console.log(`Perimeter: ${shape.perimeter()}`);
}

// Using interfaces for polymorphism
const circle = new Circle(5);
const rectangle = new Rectangle(4, 6);

printShapeInfo(circle);
printShapeInfo(rectangle);

// Array of shapes
const shapes: Shape[] = [circle, rectangle];
shapes.forEach(shape => {
    console.log(`Area: ${shape.area()}`);
});
```
*TypeScript uses interfaces for defining contracts that classes can implement, enabling polymorphism.*

**Rust:**
```rust
use std::f64::consts::PI;

// Trait definition
trait Shape {
    fn area(&self) -> f64;
    fn perimeter(&self) -> f64;
}

// Struct for Circle
struct Circle {
    radius: f64,
}

// Implementing the trait
impl Shape for Circle {
    fn area(&self) -> f64 {
        PI * self.radius * self.radius
    }
    
    fn perimeter(&self) -> f64 {
        2.0 * PI * self.radius
    }
}

// Struct for Rectangle
struct Rectangle {
    width: f64,
    height: f64,
}

// Implementing the trait
impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.width * self.height
    }
    
    fn perimeter(&self) -> f64 {
        2.0 * (self.width + self.height)
    }
}

// Function that accepts any Shape (trait object)
fn print_shape_info(shape: &dyn Shape) {
    println!("Area: {}", shape.area());
    println!("Perimeter: {}", shape.perimeter());
}

(Content truncated due to size limit. Use line ranges to read in chunks)
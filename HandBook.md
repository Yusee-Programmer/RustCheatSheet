# 🦀 THE COMPLETE RUST HANDBOOK

**From Beginner to Advanced Rust Developer in 2 Months**

A Comprehensive, Beginner-Friendly Guide to Learning Rust with Real-World Examples

---

## 📚 Table of Contents

1. [Introduction to Rust](#introduction-to-rust)
2. [Phase 1: Rust Basics & Foundations](#phase-1-rust-basics--foundations)
3. [Phase 2: Ownership & Memory System](#phase-2-ownership--memory-system)
4. [Phase 3: Data Structures & Error Handling](#phase-3-data-structures--error-handling)
5. [Phase 4: Traits & Type System](#phase-4-traits--type-system)
6. [Phase 5: Advanced Patterns & Async](#phase-5-advanced-patterns--async)
7. [Phase 6: Concurrency Mastery](#phase-6-concurrency-mastery)
8. [Phase 7: Production & Optimization](#phase-7-production--optimization)

---

## Introduction to Rust

### What is Rust?

Rust is a **systems programming language** that lets you write fast, safe code. It's like the security guard of programming languages—it watches over you and prevents you from making dangerous mistakes at compile time (before your code even runs).

**Think of it this way:**
- **C/C++**: Very fast, but you can shoot yourself in the foot 🔫
- **Python/JavaScript**: Very easy, but runs slower 🐢
- **Rust**: Fast AND safe—the best of both worlds ⚡🛡️

### The Three Pillars of Rust

| Pillar | What It Means | Why It Matters |
|--------|--------------|---|
| **Safety** | No crashes, no data races | Code you can trust in production |
| **Speed** | No garbage collector overhead | Performance like C/C++ |
| **Concurrency** | Fearless multithreading | Safe parallel programs |

### Why Learn Rust?

✅ **Memory safety** without runtime overhead
✅ **Zero-cost abstractions** — high-level code, low-level performance
✅ **No garbage collector** — predictable performance
✅ **Excellent error messages** — the compiler helps you learn
✅ **Growing ecosystem** — used at AWS, Discord, Mozilla, Microsoft, Google

### Real-World Uses

- **Operating Systems**: Linux modules, embedded systems, microcontrollers
- **Web Servers**: Axum, Actix-web, Rocket (millions of requests/second)
- **Blockchain**: Solana, Near, Polkadot
- **Cloud**: AWS lambdas, Cloudflare Workers
- **CLI Tools**: Ripgrep, Exa, Starship

---

# PHASE 1: Rust Basics & Foundations

## 🎯 What You'll Learn

Understanding the fundamental building blocks: variables, data types, control flow, and functions.

---

## 1️⃣ Variables and Immutability

### The Concept: Immutable by Default

In Rust, variables are **immutable by default**. This means once you assign a value, it cannot be changed.

**Why?** 
- Prevents accidental changes
- Makes code easier to understand
- Helps the compiler optimize your code
- Crucial for multithreading safety

### Example: Immutable Variables

```rust
// ✅ This compiles
let x = 5;
println!("x = {}", x);  // 5

// ❌ This does NOT compile - error!
x = 6;  // Cannot assign twice to immutable variable
```

### Making Variables Mutable

Use the `mut` keyword:

```rust
let mut x = 5;
println!("x = {}", x);  // 5

x = 6;  // ✅ Now it works
println!("x = {}", x);  // 6
```

**When to use `mut`?**
- When you need to change a value over time
- Counters, accumulators, state that changes

### Constants vs Variables

```rust
// Constants - ALWAYS immutable, never mut
// Must have type annotation, value MUST be known at compile time
const MAX_USERS: u32 = 100;  // Screaming_snake_case
const PI: f64 = 3.14159;

// Variables - can be mutable
let x = 5;
let mut y = 5;
```

**Difference:**
| Feature | `let` Variables | `const` |
|---------|---|---|
| Immutability | Default, but can use `mut` | Always immutable |
| Type | Usually inferred | Must be explicit |
| Value | Can be any expression | Must be compile-time constant |
| Scope | Block scope | Global or module scope |

### Shadowing: Reusing Variable Names

Shadowing means declaring a new variable with the same name as a previous variable.

```rust
let x = 5;              // x = 5
let x = x + 1;          // x = 6 (shadow previous x)
let x = x * 2;          // x = 12 (shadow again)
println!("x = {}", x);  // 12

// Shadowing can change TYPE!
let x = "hello";        // x is now &str, not i32
println!("x = {}", x);  // hello
```

**Shadowing vs Mutability:**

```rust
// Method 1: Shadowing (changes type)
let spaces = "   ";
let spaces = spaces.len();  // &str → usize

// Method 2: Mutability (same type)
let mut spaces = 3;
spaces = spaces + 5;  // 3 → 8, still u32
```

**When to use shadowing:**
- Transforming a value's type
- Converting between representations
- Cleaner than having multiple variable names

---

## 2️⃣ Data Types

Rust is **statically typed**—every value has a specific type, but often Rust infers it.

### Scalar Types (Single Values)

#### Integers

Integers come in different sizes and can be signed (negative) or unsigned (positive only):

```rust
// Signed integers (can be negative)
let small_number: i8 = -10;      // -128 to 127
let medium_number: i32 = 42;     // Most common
let large_number: i64 = 1000;    // Very large numbers

// Unsigned integers (only positive)
let positive: u32 = 100;         // 0 to 4,294,967,295
let another: u8 = 255;           // 0 to 255

// Let Rust infer (defaults to i32)
let x = 5;                       // i32
let y = 5u32;                    // Explicit u32
let z = 5_000;                   // Underscores for readability (same as 5000)

// Different number formats
let hex = 0xff;                  // Hexadecimal
let octal = 0o77;                // Octal
let binary = 0b1111_0000;        // Binary

// Integer overflow in debug vs release
let mut x: u8 = 255;
x += 1;                          // Panics in debug, wraps in release
```

**When to use each integer type?**
- `i32`: Default choice for most cases
- `u32`, `u64`: When you know values are positive (IDs, counts)
- `i8`, `i16`: Saving memory (rare)
- `i64`, `u64`: Very large numbers
- `isize`, `usize`: Array/vector indexing

#### Floating-Point Numbers

```rust
let x = 2.0;              // f64 by default (double precision)
let y: f32 = 3.0;         // f32 (single precision)

// Math operations
let sum = 5.0 + 6.0;
let difference = 95.5 - 4.3;
let product = 4.0 * 30.0;
let quotient = 56.7 / 32.2;
let modulo = 5 % 3;
```

**When to use?**
- `f64`: Default, most accurate
- `f32`: Saving memory (rare)

#### Booleans

```rust
let t = true;
let f: bool = false;

// Used in conditions
if t {
    println!("It's true!");
}

// Logical operations
let result = true && false;  // false (AND)
let result = true || false;  // true (OR)
let result = !true;          // false (NOT)
```

#### Characters

```rust
let c = 'z';
let heart = '❤';
let emoji = '😀';  // Any Unicode character!

// Note: char is 4 bytes (full Unicode)
let size = std::mem::size_of::<char>();  // 4
```

### Compound Types (Multiple Values)

#### Tuples: Fixed Size, Different Types

```rust
// Create a tuple
let tup: (i32, f64, u8) = (500, 6.4, 1);

// Access by position using dot notation
let first = tup.0;   // 500
let second = tup.1;  // 6.4
let third = tup.2;   // 1

// Destructuring: unpack into separate variables
let (x, y, z) = tup;
println!("x = {}, y = {}, z = {}", x, y, z);

// Unit type: empty tuple (represents "nothing")
let unit: () = ();
```

**When to use tuples:**
- Returning multiple values from a function
- Grouping related values with different types
- Temporary grouping

#### Arrays: Fixed Size, Same Type

```rust
// Array of 5 integers
let arr = [1, 2, 3, 4, 5];

// Explicit type and length
let arr: [i32; 5] = [1, 2, 3, 4, 5];

// Array with same value repeated
let zeros = [0; 10];  // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

// Access elements
let first = arr[0];    // 1
let last = arr[4];     // 5

// Get length
let length = arr.len();  // 5

// Iterate
for element in &arr {
    println!("{}", element);
}
```

**When to use arrays:**
- Fixed-size collections known at compile time
- Stack allocation (fast)
- When you don't need to resize

---

## 3️⃣ Control Flow

Control flow is how your program makes decisions and repeats actions.

### If Expressions

```rust
let number = 6;

if number % 4 == 0 {
    println!("divisible by 4");
} else if number % 3 == 0 {
    println!("divisible by 3");  // This prints
} else {
    println!("not divisible");
}
```

**Key point: `if` is an EXPRESSION, not just a statement**

```rust
// if returns a value!
let number = 6;
let message = if number % 2 == 0 {
    "even"
} else {
    "odd"
};
println!("{}", message);  // "even"
```

**Important:** All branches must return the same type:

```rust
// ❌ Won't compile - different types
let x = if condition { 5 } else { "six" };

// ✅ Compiles - same type
let x = if condition { 5 } else { 6 };
```

### Loops

#### `loop`: Infinite Loop (Until Break)

```rust
let mut count = 0;
let result = loop {
    count += 1;
    if count == 10 {
        break count * 2;  // Break with value
    }
};
println!("Result: {}", result);  // 20

// Simple infinite loop (dangerous!)
loop {
    println!("again!");
}  // Never stops
```

#### `while`: Loop While Condition Is True

```rust
let mut number = 3;
while number != 0 {
    println!("{}!", number);
    number -= 1;
}
// Output: 3! 2! 1!
```

**When to use `while`:**
- You don't know how many iterations
- Condition is complex
- Reading from input

#### `for`: The Most Common Loop

```rust
// Loop a specific number of times
for i in 1..5 {  // 1, 2, 3, 4 (5 is excluded)
    println!("{}", i);
}

// Inclusive range
for i in 1..=5 {  // 1, 2, 3, 4, 5
    println!("{}", i);
}

// Loop over collection
let arr = [10, 20, 30];
for element in arr {
    println!("{}", element);
}

// Loop with index
for (index, value) in arr.iter().enumerate() {
    println!("arr[{}] = {}", index, value);
}

// Loop in reverse
for i in (1..4).rev() {  // 3, 2, 1
    println!("{}", i);
}
```

**Why `for` is best:**
- Cleaner syntax
- No manual index management
- Works with any iterator
- Cannot accidentally create infinite loop

### Pattern Matching with `match`

The `match` expression is like a switch statement on steroids—it's one of Rust's superpowers!

```rust
let number = 5;

match number {
    1 => println!("One"),
    2 => println!("Two"),
    3 => println!("Three"),
    4 => println!("Four"),
    5 => println!("Five"),  // This matches
    _ => println!("Something else"),  // Catch-all pattern
}

// match with range
match number {
    1..=5 => println!("Between 1 and 5"),
    6..=10 => println!("Between 6 and 10"),
    _ => println!("Something else"),
}

// match with multiple conditions (OR pattern)
match number {
    1 | 2 => println!("One or two"),
    3 | 4 => println!("Three or four"),
    _ => println!("Other"),
}

// match with guard (additional condition)
match number {
    n if n % 2 == 0 => println!("Even number"),
    _ => println!("Odd number"),
}

// match returns a value
let message = match number {
    1 => "one",
    2 => "two",
    _ => "other",
};
```

**Why `match` is powerful:**
- Exhaustive—compiler checks all cases
- Type-safe
- Clear intent
- Can extract values

### `if let` and `while let`: Shorthand Matching

Use these when you only care about one pattern:

```rust
let optional = Some(5);

// Instead of this (verbose)
match optional {
    Some(x) => println!("Found: {}", x),
    None => {}
}

// Use this (concise)
if let Some(x) = optional {
    println!("Found: {}", x);
} else {
    println!("Nothing");
}

// while let for looping through Option/Result
let mut stack = vec![1, 2, 3];
while let Some(top) = stack.pop() {
    println!("{}", top);  // 3, 2, 1
}
```

---

## 4️⃣ Functions

Functions are reusable blocks of code.

### Basic Function Syntax

```rust
fn function_name(parameter: Type) -> ReturnType {
    // Function body
    value  // Implicit return (no semicolon!)
}

// Example: Add two numbers
fn add(a: i32, b: i32) -> i32 {
    a + b  // Implicit return
}

// Example: Explicit return
fn add_explicit(a: i32, b: i32) -> i32 {
    return a + b;  // Explicit return (has semicolon)
}

// Example: No return value (returns unit ())
fn print_number(x: i32) {
    println!("The number is {}", x);
}

// Multiple parameters
fn add_three_numbers(x: i32, y: i32, z: i32) -> i32 {
    x + y + z
}

// No parameters
fn hello() {
    println!("Hello!");
}
```

### Statements vs Expressions

**Statements:** Perform an action but don't return a value (end with `;`)
**Expressions:** Evaluate to a value and can be returned (no `;`)

```rust
// Statement: no return value
let x = 5;  // Statement

// Expression: returns a value
let y = {
    let x = 3;
    x + 1  // No semicolon! Returns 4
};
println!("{}", y);  // 4

// Same code with semicolon becomes statement (returns nothing)
let z = {
    let x = 3;
    x + 1;  // Semicolon makes it statement!
};  // z has type ()

// Expression in if
let number = 6;
let message = if number % 2 == 0 { "even" } else { "odd" };
```

### Function Placement

```rust
fn main() {
    let result = add(5, 3);
    println!("{}", result);  // 8
}

fn add(a: i32, b: i32) -> i32 {
    a + b  // You can define functions after main!
}
```

---

## 🎓 Phase 1 Summary

✅ **Variables**: Immutable by default, use `mut` when needed
✅ **Data Types**: Scalars (i32, f64, bool, char) and Compounds (tuples, arrays)
✅ **Control Flow**: `if`, `loop`, `while`, `for`, `match`
✅ **Functions**: Declare with `fn`, parameters need type annotations, return type after `->`

**Practice Project:** Build a simple calculator with functions for add, subtract, multiply, divide.

---

# PHASE 2: Ownership & Memory System

## 🎯 What You'll Learn

The core concept that makes Rust safe: ownership and borrowing. This is what separates Rust from other languages.

---

## 1️⃣ The Ownership System

### The Three Rules of Ownership

Rust enforces three rules:

1. **Each value has exactly one owner**
2. **When the owner goes out of scope, the value is freed**
3. **You can transfer ownership (move) or borrow**

### Rule 1: Each Value Has One Owner

```rust
let s1 = String::from("hello");  // s1 is owner
let s2 = s1;                     // Ownership moves to s2
// println!("{}", s1);           // ❌ s1 no longer owner - ERROR!
println!("{}", s2);              // ✅ s2 is owner - OK
```

**Why?** Prevents double-free errors (freeing same memory twice).

### Rule 2: Scope = Lifetime

```rust
{
    let s = String::from("hello");
    println!("{}", s);  // Works
}  // s goes out of scope here - memory freed
// println!("{}", s);  // ❌ s is gone - ERROR!
```

### Rule 3: Transfer Ownership with Move

```rust
fn takes_ownership(s: String) {
    println!("{}", s);
}  // s goes out of scope, memory freed

fn main() {
    let s = String::from("hello");
    takes_ownership(s);  // Ownership moves to function
    // println!("{}", s);  // ❌ ERROR - s doesn't own anymore
}
```

### Stack vs Heap: Understanding Memory

| Stack | Heap |
|-------|------|
| Fixed size | Dynamic size |
| Known at compile time | Unknown at compile time |
| LIFO (Last In First Out) | Can be accessed anywhere |
| Automatic cleanup | Need to manage manually |
| Super fast | Slower |
| **Example**: i32, bool, tuples of fixed types | **Example**: String, Vec, HashMap |

```rust
// Stack: Just a number, moved by copying
let x = 5;
let y = x;  // x is COPIED (not moved)
println!("{}", x);  // ✅ x still exists

// Heap: Complex data, moved (not copied)
let s1 = String::from("hello");  // Allocates on heap
let s2 = s1;  // Ownership MOVED, not copied
// println!("{}", s1);  // ❌ ERROR
```

### Copy Trait: Implicit Copying

Some types implement `Copy` trait, which means they're copied instead of moved:

```rust
// These types implement Copy - automatically copied
let x = 5;
let y = x;  // Copied, not moved
println!("{}", x);  // ✅ Works

// String does NOT implement Copy
let s1 = String::from("hello");
let s2 = s1;  // Moved
// println!("{}", s1);  // ❌ ERROR
```

**Types that implement Copy:**
- All integers: i32, u64, etc.
- Floating-point: f32, f64
- Boolean: bool
- Character: char
- Tuples of Copy types: (i32, bool)

**Types that DON'T implement Copy:**
- String
- Vec
- HashMap
- Everything else complex

---

## 2️⃣ References & Borrowing

Instead of moving ownership, you can **borrow** by using references.

### Immutable References (`&T`)

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
}  // s reference goes out of scope but doesn't own

fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);  // Borrow with &
    println!("'{}' has length {}", s1, len);  // s1 still valid!
}
```

**Borrowing with immutable references:**
- You keep ownership
- Function can read but not modify
- You can have unlimited immutable references

```rust
let s = String::from("hello");
let r1 = &s;   // ✅ Multiple immutable refs allowed
let r2 = &s;
let r3 = &s;
println!("{}, {}, {}", r1, r2, r3);  // All work
```

### Mutable References (`&mut T`)

```rust
fn append_world(s: &mut String) {
    s.push_str(" world");  // Can modify!
}

fn main() {
    let mut s = String::from("hello");
    append_world(&mut s);  // Borrow mutably
    println!("{}", s);  // "hello world"
}
```

**Important Rule: You can have only ONE mutable reference**

```rust
let mut s = String::from("hello");

let r1 = &mut s;  // ✅ First mutable ref
// let r2 = &mut s;  // ❌ ERROR - can't have two!

println!("{}", r1);
// r1 is last used here, so now we could borrow again
let r2 = &mut s;  // ✅ Now OK - r1 is done
println!("{}", r2);
```

**Why this rule?**
- Prevents data races at compile time
- Only one person can change data at a time
- Essential for multithreading safety

### Can't Mix Immutable and Mutable References

```rust
let mut s = String::from("hello");

let r1 = &s;      // ✅ Immutable
let r2 = &s;      // ✅ Another immutable
// let r3 = &mut s;  // ❌ ERROR - can't mix!

println!("{}, {}", r1, r2);  // Use immutables
// r1 and r2 go out of scope here

let r3 = &mut s;  // ✅ Now OK - no more immutable refs
println!("{}", r3);
```

### The Golden Rules of References

**For Immutable References:**
- Can have many at once
- Cannot modify data

**For Mutable References:**
- Can have only one at a time
- Can modify data

**Mixed:**
- Cannot have both immutable and mutable at the same time

```rust
// ✅ Multiple immutable
let r1 = &s;
let r2 = &s;

// ❌ Mix immutable and mutable
let mut_r = &mut s;
let immut_r = &s;  // ERROR - can't mix

// ✅ One mutable, no immutable
let mut_r1 = &mut s;
let mut_r2 = &mut s;  // ERROR - only one mutable

let mut_r = &mut s;  // OK
```

---

## 3️⃣ Lifetimes

Lifetimes ensure that references stay valid. The compiler uses lifetimes to prevent dangling references.

### Dangling References (Prevented by Compiler)

```rust
// ❌ This won't compile - dangling reference!
fn dangle() -> &String {
    let s = String::from("hello");
    &s  // s is freed when function ends!
}  // Reference would point to freed memory
```

### Lifetime Annotations

When Rust can't infer lifetimes, you must annotate them:

```rust
// Without annotation - ambiguous
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() { x } else { y }
}

// ERROR: Rust doesn't know which reference to return!
// Is it borrowed from x or y? How long should it live?

// With annotation - clear
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

**Meaning of `'a`:**
- `'a` is a lifetime parameter (like `<T>` for types)
- It means: "The returned reference will be valid as long as both input references are valid"

### Reading Lifetimes

```rust
fn example<'a>(x: &'a str, y: &str) -> &'a str {
    // Return references lasts as long as x
    // y doesn't have lifetime annotation - that's OK if not returned
}

struct Excerpt<'a> {
    part: &'a str,
}

// struct needs lifetime annotation if it holds references
```

### Lifetime Elision Rules

Rust can infer lifetimes in many cases:

```rust
// Rust infers lifetime automatically
fn first_word(s: &str) -> &str {
    // Equivalent to:
    // fn first_word<'a>(s: &'a str) -> &'a str
}

// When there's only one input reference, the output reference
// automatically gets the same lifetime
```

### The `'static` Lifetime

`'static` means the reference lives for the entire program:

```rust
let s: &'static str = "hello";  // String literal

const CONST_STR: &'static str = "constant";
```

---

## 4️⃣ Smart Pointers

Smart pointers are types that act like pointers but have superpowers (extra metadata and behavior).

### `Box<T>`: Heap Allocation

Box stores data on the heap instead of the stack.

```rust
// Stack allocation (too big?)
let v = vec![1, 2, 3];  // On stack

// Heap allocation (explicit)
let b = Box::new(42);
println!("{}", b);  // 42 - dereference automatically

// Use case: Recursive types need Box
enum List {
    Cons(i32, Box<List>),  // Box breaks infinite size
    Nil,
}

let list = List::Cons(1, Box::new(List::Nil));
```

**When to use Box:**
- Recursive data structures
- Large data (move to heap for performance)
- Trait objects (covered later)

### `Rc<T>`: Shared Ownership (Single-threaded)

Rc = Reference Counted. Multiple owners of same data.

```rust
use std::rc::Rc;

let shared = Rc::new(String::from("hello"));
let owner1 = Rc::clone(&shared);  // Clone reference count
let owner2 = Rc::clone(&shared);

println!("Count: {}", Rc::strong_count(&shared));  // 3

// When last owner drops, data is freed
```

**When to use Rc:**
- Multiple owners, single-threaded
- Tree/graph structures
- Complex ownership patterns

### `RefCell<T>`: Interior Mutability

RefCell allows mutation through immutable reference (enforces borrow rules at runtime):

```rust
use std::cell::RefCell;

struct Logger {
    logs: RefCell<Vec<String>>,
}

impl Logger {
    fn log(&self, msg: String) {  // Note: &self, not &mut self
        self.logs.borrow_mut().push(msg);  // But we can still mutate!
    }
}

let logger = Logger { logs: RefCell::new(Vec::new()) };
logger.log("Event 1".to_string());
```

**When to use RefCell:**
- Need mutability through immutable reference
- Borrow checking at runtime (panics if violated)
- Single-threaded only

### `Arc<T>`: Shared Ownership (Multi-threaded)

Arc = Atomic Reference Counted. Thread-safe version of Rc.

```rust
use std::sync::Arc;
use std::thread;

let shared = Arc::new(42);

for _ in 0..3 {
    let shared = Arc::clone(&shared);
    thread::spawn(move || {
        println!("{}", shared);
    });
}
```

**When to use Arc:**
- Multiple threads need same data
- Sharing data across threads safely

### Combining Smart Pointers

```rust
use std::rc::Rc;
use std::cell::RefCell;

let shared = Rc::new(RefCell::new(5));
let owner1 = Rc::clone(&shared);

*owner1.borrow_mut() = 10;
println!("{}", shared.borrow());  // 10
```

---

## 🎓 Phase 2 Summary

✅ **Ownership**: Each value has one owner, moves when assigned
✅ **Borrowing**: Use `&` for immutable, `&mut` for mutable references
✅ **References**: One mutable reference OR unlimited immutable references
✅ **Lifetimes**: Ensure references stay valid
✅ **Smart Pointers**: Box, Rc, RefCell, Arc for special ownership patterns

**Practice Project:** Implement a simple linked list using Box and recursive enums.

---

# PHASE 3: Data Structures & Error Handling

## 🎯 What You'll Learn

Collections (Vec, HashMap, String) and proper error handling with Result and Option.

---

## 1️⃣ Vectors (`Vec<T>`)

Vectors are dynamic, growable arrays on the heap.

### Creating Vectors

```rust
// Create empty vector
let mut v: Vec<i32> = Vec::new();

// Create with initial values
let v = vec![1, 2, 3];

// Create with capacity
let mut v = Vec::with_capacity(100);  // Allocate space for 100

// Type inferred from values
let v = vec![1, 2, 3];  // Vec<i32>
let v = vec!["a", "b"];  // Vec<&str>
```

### Adding Elements

```rust
let mut v = Vec::new();
v.push(5);
v.push(6);
v.push(7);
println!("{:?}", v);  // [5, 6, 7]

v.insert(0, 0);  // Insert at index 0
println!("{:?}", v);  // [0, 5, 6, 7]
```

### Accessing Elements

```rust
let v = vec![1, 2, 3];

// Index access - panics if out of bounds
let third = &v[2];  // 3

// Safe access with get
if let Some(third) = v.get(2) {
    println!("{}", third);  // 3
}

if let Some(fifth) = v.get(4) {
    println!("{}", fifth);
} else {
    println!("No element at index 4");  // This prints
}
```

### Modifying Elements

```rust
let mut v = vec![1, 2, 3];

v[0] = 10;  // Change first element
println!("{:?}", v);  // [10, 2, 3]

if let Some(first) = v.get_mut(0) {
    *first *= 2;  // Multiply by 2
}
println!("{:?}", v);  // [20, 2, 3]
```

### Removing Elements

```rust
let mut v = vec![1, 2, 3, 4, 5];

let last = v.pop();  // Remove and return last
println!("{:?}", last);  // Some(5)

v.remove(2);  // Remove at index 2
println!("{:?}", v);  // [1, 2, 4]

v.clear();  // Remove all
println!("{:?}", v);  // []
```

### Iterating Over Vectors

```rust
let v = vec![1, 2, 3];

// Immutable iteration
for element in &v {
    println!("{}", element);
}

// Mutable iteration
let mut v = vec![1, 2, 3];
for element in &mut v {
    *element *= 2;  // Multiply by 2
}
println!("{:?}", v);  // [2, 4, 6]

// Consuming iteration (takes ownership)
let v = vec![1, 2, 3];
for element in v {  // v moved here
    println!("{}", element);
}
// println!("{:?}", v);  // ❌ v is gone
```

### Vector Methods

```rust
let v = vec![3, 1, 4, 1, 5, 9];

// Length
println!("{}", v.len());  // 6

// Sorting
let mut v = v.clone();
v.sort();
println!("{:?}", v);  // [1, 1, 3, 4, 5, 9]

// Reverse sorting
v.sort_by(|a, b| b.cmp(a));
println!("{:?}", v);  // [9, 5, 4, 3, 1, 1]

// Contains
println!("{}", v.contains(&5));  // true

// Filtering and mapping (covered in Phase 5)
```

---

## 2️⃣ HashMaps (`HashMap<K, V>`)

Key-value storage with O(1) average access time.

### Creating HashMaps

```rust
use std::collections::HashMap;

// Create empty
let mut scores = HashMap::new();

// Insert values
scores.insert("Alice".to_string(), 100);
scores.insert("Bob".to_string(), 85);

// Create from array of tuples
let scores = HashMap::from([
    ("Alice".to_string(), 100),
    ("Bob".to_string(), 85),
]);

// Create from Vec
let keys = vec!["Alice", "Bob"];
let values = vec![100, 85];
let scores: HashMap<_, _> = keys.into_iter().zip(values.into_iter()).collect();
```

### Accessing Values

```rust
let mut scores = HashMap::new();
scores.insert("Alice".to_string(), 100);

// Get value - returns Option
if let Some(score) = scores.get("Alice") {
    println!("Alice's score: {}", score);
}

// Get with default
let score = scores.get("Charlie").unwrap_or(&0);
println!("Charlie's score: {}", score);  // 0
```

### Inserting and Updating

```rust
let mut scores = HashMap::new();

// Simple insert
scores.insert("Alice".to_string(), 100);

// Insert only if key doesn't exist
scores.entry("Bob".to_string()).or_insert(85);
scores.entry("Bob".to_string()).or_insert(90);  // Not inserted, Bob still 85

// Update if exists, insert if not
scores.entry("Charlie".to_string())
    .and_modify(|score| *score += 10)
    .or_insert(75);
```

### Removing Values

```rust
let mut scores = HashMap::new();
scores.insert("Alice".to_string(), 100);

// Remove and get value
if let Some(score) = scores.remove("Alice") {
    println!("Removed Alice with score {}", score);
}

// Check if empty
println!("{}", scores.is_empty());  // false -> true
```

### Iterating

```rust
let scores = HashMap::from([
    ("Alice".to_string(), 100),
    ("Bob".to_string(), 85),
]);

// Iterate over key-value pairs
for (name, score) in &scores {
    println!("{}: {}", name, score);
}

// Iterate over keys
for name in scores.keys() {
    println!("{}", name);
}

// Iterate over values
for score in scores.values() {
    println!("{}", score);
}
```

### Counting Pattern

A very common pattern:

```rust
let text = "hello world hello rust";
let mut counts = HashMap::new();

for word in text.split_whitespace() {
    *counts.entry(word).or_insert(0) += 1;
}

for (word, count) in &counts {
    println!("{}: {}", word, count);
}
// hello: 2, world: 1, rust: 1
```

---

## 3️⃣ Strings

Two main string types: `String` (owned) and `&str` (borrowed).

### String vs &str

```rust
// &str - string literal or slice
let literal: &str = "Hello";  // String literal
let slice: &str = &String::from("Hello")[0..5];  // Slice

// String - owned, mutable, heap-allocated
let s = String::new();  // Empty
let s = String::from("Hello");  // From literal
let s = "Hello".to_string();  // Convert from &str

// String is like Vec<u8> with UTF-8 guarantee
```

### String Operations

```rust
let mut s = String::from("Hello");

// Add character
s.push(' ');

// Add string slice
s.push_str("World");
println!("{}", s);  // "Hello World"

// Insert character at index
s.insert(5, '-');
println!("{}", s);  // "Hello- World"

// Remove character at index
s.remove(5);
println!("{}", s);  // "Hello World"

// Remove and return last character
if let Some(c) = s.pop() {
    println!("{}", c);  // 'd'
}

// Truncate to specific length
s.truncate(5);
println!("{}", s);  // "Hello"

// Clear all
s.clear();
println!("{}", s);  // ""
```

### String Concatenation

```rust
let s1 = String::from("Hello");
let s2 = String::from("World");

// Method 1: Using + operator (s1 is moved!)
let s3 = s1 + " " + &s2;
// println!("{}", s1);  // ❌ s1 moved

// Method 2: Using format! macro (no moves)
let s3 = format!("{} {}", s1, s2);
println!("{}", s1);  // ✅ s1 still valid
```

### String Searching

```rust
let s = "Hello World";

// Contains substring
println!("{}", s.contains("World"));  // true

// Starts/ends with
println!("{}", s.starts_with("Hello"));  // true
println!("{}", s.ends_with("d"));  // true

// Find index
if let Some(index) = s.find("World") {
    println!("Found at index {}", index);  // 6
}
```

### String Splitting

```rust
let s = "hello,world,rust";

// Split by character
for word in s.split(',') {
    println!("{}", word);  // hello, world, rust
}

// Split whitespace
let s = "hello world rust";
for word in s.split_whitespace() {
    println!("{}", word);
}

// Collect into vector
let words: Vec<&str> = s.split(',').collect();
```

### String Iteration

```rust
let s = "hello";

// Iterate over characters (Unicode)
for c in s.chars() {
    println!("{}", c);  // h, e, l, l, o
}

// Iterate over bytes
for b in s.bytes() {
    println!("{}", b);  // ASCII values
}

// String is UTF-8, so characters can be multi-byte
let s = "中文";
println!("{}", s.len());  // 6 (3 bytes per character)
for c in s.chars() {
    println!("{}", c);  // 中, 文
}
```

---

## 4️⃣ Error Handling

Rust handles errors explicitly with `Result` and `Option`.

### `Option<T>`: When Value May or May Not Exist

```rust
enum Option<T> {
    Some(T),  // Value exists
    None,     // Value doesn't exist
}

// Using Option
let x = Some(5);
let y: Option<i32> = None;

// Pattern matching
match x {
    Some(value) => println!("Value: {}", value),
    None => println!("No value"),
}

// if let shorthand
if let Some(value) = x {
    println!("Value: {}", value);
}
```

### `Result<T, E>`: Recoverable Errors

```rust
enum Result<T, E> {
    Ok(T),     // Success, contains value
    Err(E),    // Error, contains error info
}

// Function returning Result
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

// Using Result with match
match divide(10.0, 2.0) {
    Ok(result) => println!("Result: {}", result),
    Err(e) => println!("Error: {}", e),
}

// Using Result with if let
if let Ok(result) = divide(10.0, 2.0) {
    println!("Result: {}", result);
}
```

### The `?` Operator: Error Propagation

The `?` operator makes error handling clean:

```rust
use std::fs::File;
use std::io::{self, Read};

// Without ?
fn read_file_old() -> Result<String, io::Error> {
    match File::open("file.txt") {
        Ok(mut file) => {
            let mut contents = String::new();
            match file.read_to_string(&mut contents) {
                Ok(_) => Ok(contents),
                Err(e) => Err(e),
            }
        }
        Err(e) => Err(e),
    }
}

// With ? (much cleaner!)
fn read_file() -> Result<String, io::Error> {
    let mut file = File::open("file.txt")?;  // If error, return it
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;  // If error, return it
    Ok(contents)  // Success
}

// Using it
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let contents = read_file()?;
    println!("{}", contents);
    Ok(())
}
```

**How `?` works:**
- If `Result` is `Err`, immediately returns that error
- If `Result` is `Ok(value)`, extracts the value and continues
- Can only use `?` in functions that return `Result` or `Option`

### Custom Error Types

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
    
    #[error("Parse error: {0}")]
    Parse(#[from] serde_json::Error),
    
    #[error("Invalid input: {0}")]
    InvalidInput(String),
    
    #[error("Not found")]
    NotFound,
}

// Using custom error
fn process_data(input: &str) -> Result<Data, AppError> {
    let data = serde_json::from_str(input)?;  // Automatically converts to AppError
    validate_data(&data)?;
    Ok(data)
}
```

---

## 🎓 Phase 3 Summary

✅ **Vectors**: Dynamic arrays with push, pop, iteration
✅ **HashMaps**: Key-value storage with insert, get, remove
✅ **Strings**: UTF-8 encoded, owned (String) or borrowed (&str)
✅ **Option**: For values that may or may not exist
✅ **Result**: For recoverable errors
✅ **? Operator**: Clean error propagation

**Practice Project:** Build a contact manager with Vec and HashMap, proper error handling.

---

# PHASE 4: Traits & Type System

## 🎯 What You'll Learn

Traits define shared behavior. Generics write code for many types. This is power!

---

## 1️⃣ Traits: Defining Shared Behavior

A trait is like a contract saying "types implementing me must have these methods."

### Defining Traits

```rust
// Define a trait
trait Drawable {
    fn draw(&self);
    
    fn name(&self) -> &str;
    
    // Default implementation
    fn describe(&self) -> String {
        format!("Shape: {}", self.name())
    }
}

// Implement trait for Circle
struct Circle {
    radius: f64,
}

impl Drawable for Circle {
    fn draw(&self) {
        println!("Drawing circle with radius {}", self.radius);
    }
    
    fn name(&self) -> &str {
        "Circle"
    }
    // describe() uses default implementation
}

// Implement trait for Square
struct Square {
    side: f64,
}

impl Drawable for Square {
    fn draw(&self) {
        println!("Drawing square with side {}", self.side);
    }
    
    fn name(&self) -> &str {
        "Square"
    }
}

// Using traits
fn main() {
    let circle = Circle { radius: 5.0 };
    circle.draw();
    println!("{}", circle.describe());
}
```

### Trait Bounds: Requiring Traits

When you have generic code, you can require types to implement certain traits:

```rust
// Generic function with trait bound
fn print_shape<T: Drawable>(shape: T) {
    shape.draw();
    println!("Description: {}", shape.describe());
}

// Multiple trait bounds
fn print_and_clone<T: Drawable + Clone>(shape: T) {
    shape.draw();
    let clone = shape.clone();
    clone.draw();
}

// Using where clause (cleaner for multiple bounds)
fn process<T, U>(a: T, b: U) 
where
    T: Drawable + Clone,
    U: ToString,
{
    a.draw();
    println!("{}", b.to_string());
}
```

### Default Implementations

```rust
trait Logger {
    fn log(&self, msg: &str);
    
    // Default implementations
    fn info(&self, msg: &str) {
        println!("INFO: {}", msg);
    }
    
    fn error(&self, msg: &str) {
        println!("ERROR: {}", msg);
    }
}

struct ConsoleLogger;

impl Logger for ConsoleLogger {
    fn log(&self, msg: &str) {
        println!("LOG: {}", msg);
    }
    // info() and error() use defaults
}
```

### Derivable Traits

Rust can automatically implement common traits for you:

```rust
#[derive(Debug)]  // Enables {:?} formatting
struct Point {
    x: i32,
    y: i32,
}

#[derive(Clone, Copy)]  // Enables .clone() and implicit copying
struct Color {
    r: u8,
    g: u8,
    b: u8,
}

#[derive(PartialEq, Eq)]  // Enables == and !=
struct UserId(u32);

#[derive(Default)]  // Enables Default::default()
struct Config {
    timeout: u32,
    retries: u32,
}

let config = Config::default();  // All fields get default values
```

**Common derivable traits:**
- `Debug`: Formatting with {:?}
- `Clone`: Explicit cloning with .clone()
- `Copy`: Implicit copying
- `PartialEq`, `Eq`: Equality comparison
- `PartialOrd`, `Ord`: Ordering
- `Default`: Default values
- `Hash`: Use in HashMaps/HashSets

---

## 2️⃣ Generics: Write Once, Use Many Times

Generics let you write code that works with many types.

### Generic Functions

```rust
// Generic function
fn identity<T>(value: T) -> T {
    value
}

fn swap<T, U>(a: T, b: U) -> (U, T) {
    (b, a)
}

// Type inference
let x = identity(5);  // T inferred as i32
let y = identity("hello");  // T inferred as &str
```

### Generic Structs

```rust
// Generic struct
struct Point<T> {
    x: T,
    y: T,
}

// Create points
let int_point = Point { x: 5, y: 10 };
let float_point = Point { x: 1.0, y: 2.5 };
let string_point = Point { x: "a", y: "b" };

// Multiple type parameters
struct Pair<T, U> {
    first: T,
    second: U,
}

let pair = Pair { first: 42, second: "hello" };
```

### Implementing Generics

```rust
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

// Methods only for Point<f64>
impl Point<f64> {
    fn distance_from_origin(&self) -> f64 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

// Methods only for types that implement Clone
impl<T: Clone> Point<T> {
    fn clone_point(&self) -> Self {
        Point {
            x: self.x.clone(),
            y: self.y.clone(),
        }
    }
}
```

### Monomorphization

Generics are resolved at compile time—each concrete type gets its own copy of the code:

```rust
let integer = 5;
let float = 5.0;
```

The compiler generates:

```rust
fn identity_i32(value: i32) -> i32 { value }
fn identity_f64(value: f64) -> f64 { value }
```

**Result: Zero runtime cost, but binary can be larger.**

---

## 3️⃣ Associated Types

Associated types let traits define output types.

### Why Associated Types?

Without associated types, iterators would be awkward:

```rust
// Bad: Using generics everywhere
trait Iterator<T> {
    fn next(&mut self) -> Option<T>;
}

// Confusing: Which T does this implement?
impl Iterator<i32> for Counter { }
impl Iterator<String> for Counter { }

// Better: Using associated types
trait Iterator {
    type Item;  // Associated type
    fn next(&mut self) -> Option<Self::Item>;
}

// Clear: Counter yields u32
struct Counter {
    count: u32,
    max: u32,
}

impl Iterator for Counter {
    type Item = u32;
    
    fn next(&mut self) -> Option<Self::Item> {
        if self.count < self.max {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
```

---

## 4️⃣ Trait Objects: Runtime Polymorphism

What if you want different types in the same collection?

```rust
// Different types, but all Drawable
let shapes: Vec<Box<dyn Drawable>> = vec![
    Box::new(Circle { radius: 5.0 }),
    Box::new(Square { side: 3.0 }),
];

for shape in shapes {
    shape.draw();  // Correct method called based on type
}
```

**Static vs Dynamic Dispatch:**

```rust
// Static dispatch (compile time, faster)
fn draw<T: Drawable>(shape: T) {
    shape.draw();  // Inlined, optimized
}

// Dynamic dispatch (runtime, more flexible)
fn draw(shape: &dyn Drawable) {
    shape.draw();  // Virtual call
}
```

---

## 🎓 Phase 4 Summary

✅ **Traits**: Define shared behavior that types implement
✅ **Trait Bounds**: Require types to implement traits
✅ **Generics**: Write code once for many types
✅ **Associated Types**: Types defined by traits
✅ **Trait Objects**: Dynamic dispatch with `dyn Trait`

**Practice Project:** Implement a shape library with traits, generics, and both static and dynamic dispatch.

---

# PHASE 5: Advanced Patterns & Async

## 🎯 What You'll Learn

Iterator patterns, closures, builder pattern, and introduction to async programming.

---

## 1️⃣ Closures: Functions as Values

Closures are inline functions that can capture variables.

### Closure Syntax

```rust
// Closure syntax: |parameters| expression
let add_one = |x| x + 1;
println!("{}", add_one(5));  // 6

// With type annotations
let add = |x: i32, y: i32| -> i32 { x + y };
println!("{}", add(3, 4));  // 7

// Multiple statements use braces
let expensive = |x: i32| {
    println!("Computing...");
    x * x
};

// Capturing variables
let y = 4;
let multiply = |x| x * y;  // Captures y
println!("{}", multiply(5));  // 20
```

### Closure Types

```rust
// FnOnce: Takes ownership, can be called once
let value = String::from("hello");
let print_once = move || println!("{}", value);
print_once();
// print_once();  // ❌ Can't call twice - value moved

// FnMut: Borrows mutably, can be called multiple times
let mut x = 5;
let mut increment = || {
    x += 1;
    x
};
println!("{}", increment());  // 6
println!("{}", increment());  // 7

// Fn: Borrows immutably, can be called many times
let name = "Alice";
let greet = || println!("Hello, {}", name);
greet();  // "Hello, Alice"
greet();  // "Hello, Alice"
```

---

## 2️⃣ Iterators and Functional Programming

Rust iterators are lazy—they don't do work until you consume them.

### Creating Iterators

```rust
let v = vec![1, 2, 3];

// Immutable iterator
for element in &v {  // Calls iter() implicitly
    println!("{}", element);
}

// Mutable iterator
for element in &mut v {
    println!("{}", element);
}

// Consuming iterator (takes ownership)
for element in v {  // Calls into_iter() implicitly
    println!("{}", element);
}

// Manual iterator
let mut iter = v.iter();
println!("{:?}", iter.next());  // Some(1)
println!("{:?}", iter.next());  // Some(2)
println!("{:?}", iter.next());  // Some(3)
println!("{:?}", iter.next());  // None
```

### Iterator Adapters (map, filter, etc.)

```rust
let v = vec![1, 2, 3, 4, 5];

// map: transform each element
let doubled: Vec<_> = v.iter().map(|x| x * 2).collect();
println!("{:?}", doubled);  // [2, 4, 6, 8, 10]

// filter: keep elements that satisfy condition
let evens: Vec<_> = v.iter().filter(|x| x % 2 == 0).collect();
println!("{:?}", evens);  // [2, 4]

// chain: combine iterators
let v2 = vec![6, 7, 8];
let combined: Vec<_> = v.iter().chain(v2.iter()).copied().collect();
println!("{:?}", combined);  // [1, 2, 3, 4, 5, 6, 7, 8]

// zip: combine two iterators
let names = vec!["Alice", "Bob"];
let ages = vec![25, 30];
for (name, age) in names.iter().zip(ages.iter()) {
    println!("{} is {}", name, age);
}
```

### Consuming Iterators

```rust
let v = vec![1, 2, 3, 4, 5];

// sum: add all elements
let sum: i32 = v.iter().sum();
println!("{}", sum);  // 15

// collect: gather into collection
let doubled: Vec<_> = v.iter().map(|x| x * 2).collect();

// fold: accumulate a value
let sum = v.iter().fold(0, |acc, x| acc + x);
println!("{}", sum);  // 15

// find: get first element matching condition
if let Some(even) = v.iter().find(|x| *x % 2 == 0) {
    println!("First even: {}", even);  // 2
}

// any: check if any element matches
let has_large = v.iter().any(|x| x > &3);
println!("{}", has_large);  // true

// all: check if all elements match
let all_positive = v.iter().all(|x| x > &0);
println!("{}", all_positive);  // true
```

### Why Iterators Are Great

```rust
// Readable
let result = v.iter()
    .filter(|x| x % 2 == 0)
    .map(|x| x * x)
    .sum::<i32>();

// Lazy - nothing happens until collect/sum
let iter = v.iter().map(|x| x * 2);  // Nothing computed yet
for element in iter {  // Now it computes
    println!("{}", element);
}
```

---

## 3️⃣ Builder Pattern

When creating complex objects, builder pattern makes code cleaner:

```rust
#[derive(Clone)]
pub struct Server {
    host: String,
    port: u16,
    workers: usize,
    timeout: u64,
}

pub struct ServerBuilder {
    host: String,
    port: u16,
    workers: usize,
    timeout: u64,
}

impl ServerBuilder {
    pub fn new() -> Self {
        ServerBuilder {
            host: "localhost".to_string(),
            port: 8080,
            workers: 4,
            timeout: 30,
        }
    }
    
    pub fn host(mut self, host: &str) -> Self {
        self.host = host.to_string();
        self
    }
    
    pub fn port(mut self, port: u16) -> Self {
        self.port = port;
        self
    }
    
    pub fn workers(mut self, workers: usize) -> Self {
        self.workers = workers;
        self
    }
    
    pub fn build(self) -> Server {
        Server {
            host: self.host,
            port: self.port,
            workers: self.workers,
            timeout: self.timeout,
        }
    }
}

// Using builder
let server = ServerBuilder::new()
    .host("0.0.0.0")
    .port(3000)
    .workers(8)
    .build();
```

---

## 4️⃣ Introduction to Async Programming

Async allows handling many operations concurrently without threads.

### Async Functions

```rust
async fn fetch_user(id: u32) -> String {
    // Simulates network request
    format!("User {}", id)
}

#[tokio::main]
async fn main() {
    let user = fetch_user(1).await;
    println!("{}", user);
}
```

### Key Concepts

```rust
// Futures: Lazy computations
let future = fetch_user(1);  // Doesn't run yet

// .await: Wait for future to complete
let result = future.await;  // Now it runs

// Can't use .await outside async
fn not_async() {
    let result = fetch_user(1).await;  // ❌ ERROR
}
```

### Running Multiple Async Operations

```rust
#[tokio::main]
async fn main() {
    // Concurrent execution with tokio::join!
    let (user1, user2) = tokio::join!(
        fetch_user(1),
        fetch_user(2),
    );
    println!("{}, {}", user1, user2);
    
    // Spawning tasks
    let handle = tokio::spawn(async {
        fetch_user(3).await
    });
    
    let result = handle.await.unwrap();
    println!("{}", result);
}
```

---

## 🎓 Phase 5 Summary

✅ **Closures**: Functions as values, capturing variables
✅ **Iterators**: Lazy, composable operations on collections
✅ **Adapters**: map, filter, fold, find, sum, etc.
✅ **Builder Pattern**: Clean API for complex object creation
✅ **Async Basics**: Futures, async/await, concurrent operations

**Practice Project:** Build an async data processor using iterators and tokio.

---

# PHASE 6: Concurrency Mastery

## 🎯 What You'll Learn

Threads, message passing, shared state synchronization, and advanced async patterns.

---

## 1️⃣ Threads: Parallel Execution

### Creating Threads

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("Thread: {}", i);
            thread::sleep(Duration::from_millis(100));
        }
    });
    
    for i in 1..5 {
        println!("Main: {}", i);
        thread::sleep(Duration::from_millis(100));
    }
    
    handle.join().unwrap();  // Wait for thread to finish
}
```

### Moving Ownership

```rust
let v = vec![1, 2, 3];

let handle = thread::spawn(move || {  // move: Take ownership
    println!("{:?}", v);
});

// println!("{:?}", v);  // ❌ v moved to thread

handle.join().unwrap();
```

### Scoped Threads: Borrowing Without 'static

```rust
let data = vec![1, 2, 3];

thread::scope(|s| {
    s.spawn(|| {
        println!("Thread 1: {:?}", data);  // Can borrow!
    });
    s.spawn(|| {
        println!("Thread 2: {:?}", data);
    });
});  // Threads guaranteed to finish here

println!("{:?}", data);  // Still valid!
```

---

## 2️⃣ Message Passing: Channels

Send data between threads safely.

### Creating Channels

```rust
use std::sync::mpsc;  // Multiple Producer, Single Consumer
use std::thread;

let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    tx.send("Hello from thread".to_string()).unwrap();
});

let received = rx.recv().unwrap();
println!("{}", received);  // "Hello from thread"
```

### Multiple Messages

```rust
let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    for i in 1..4 {
        tx.send(i).unwrap();
    }
});

// recv blocks until message arrives
for received in rx {
    println!("{}", received);  // 1, 2, 3
}
```

### Multiple Producers

```rust
let (tx, rx) = mpsc::channel();
let tx2 = tx.clone();

thread::spawn(move || {
    tx.send("From thread 1").unwrap();
});

thread::spawn(move || {
    tx2.send("From thread 2").unwrap();
});

for received in rx {
    println!("{}", received);
}
```

### Sync Channels (Rendezvous)

```rust
let (tx, rx) = mpsc::sync_channel(0);  // Buffer size 0

thread::spawn(move || {
    println!("Before send");
    tx.send(42).unwrap();  // Blocks until receiver is ready
    println!("After send");
});

println!("Before receive");
let value = rx.recv().unwrap();
println!("Received: {}", value);
```

---

## 3️⃣ Shared State: Mutex and Arc

### Mutex: Exclusive Access

```rust
use std::sync::Mutex;

let counter = Mutex::new(0);

{
    let mut num = counter.lock().unwrap();  // Lock counter
    *num += 1;
}  // Lock released here

println!("{}", counter.lock().unwrap());  // 1
```

### Arc: Atomic Reference Count

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));  // Arc wraps Mutex

let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);  // Clone reference
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());  // 10
```

### RwLock: Reader-Writer Lock

```rust
use std::sync::RwLock;

let data = RwLock::new(vec![1, 2, 3]);

// Many readers
let r1 = data.read().unwrap();
let r2 = data.read().unwrap();
println!("{:?} {:?}", *r1, *r2);

// Exclusive writer
let mut w = data.write().unwrap();
w.push(4);
```

---

## 4️⃣ Advanced Async Patterns

### Tokio Channels

```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = mpsc::channel(100);
    
    tokio::spawn(async move {
        for i in 0..5 {
            tx.send(i).await.unwrap();
        }
    });
    
    while let Some(msg) = rx.recv().await {
        println!("{}", msg);
    }
}
```

### Timeouts

```rust
use tokio::time::{timeout, Duration};

#[tokio::main]
async fn main() {
    let result = timeout(
        Duration::from_secs(2),
        long_operation(),
    ).await;
    
    match result {
        Ok(_) => println!("Completed"),
        Err(_) => println!("Timeout!"),
    }
}
```

### Select: Multiple Async Operations

```rust
use tokio::select;

#[tokio::main]
async fn main() {
    select! {
        _ = operation1() => println!("Op1 finished"),
        _ = operation2() => println!("Op2 finished"),
    }
}
```

---

## 🎓 Phase 6 Summary

✅ **Threads**: Parallel execution with `thread::spawn`
✅ **Channels**: Message passing between threads
✅ **Mutex**: Exclusive access to shared data
✅ **Arc**: Atomic reference counting for shared ownership
✅ **Tokio**: Async runtime for concurrent I/O
✅ **Advanced Async**: Timeouts, select, channels

**Practice Project:** Build a multi-threaded web crawler with channels and Arc/Mutex.

---

# PHASE 7: Production & Optimization

## 🎯 What You'll Learn

Testing, documentation, performance optimization, and production patterns.

---

## 1️⃣ Testing

### Unit Tests

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
        assert_ne!(add(2, 2), 5);
        assert!(add(1, 1) == 2);
    }
    
    #[test]
    #[should_panic]
    fn test_panic() {
        panic!("This should panic");
    }
    
    #[test]
    fn test_result() -> Result<(), String> {
        if add(2, 2) == 4 {
            Ok(())
        } else {
            Err("Math is broken".to_string())
        }
    }
}
```

### Running Tests

```bash
cargo test              # Run all tests
cargo test -- --nocapture  # Show println output
cargo test test_add     # Run specific test
```

### Integration Tests

```rust
// tests/integration_test.rs
use my_crate::add;

#[test]
fn test_from_integration() {
    assert_eq!(add(2, 3), 5);
}
```

---

## 2️⃣ Documentation

### Doc Comments

```rust
/// Adds two numbers.
///
/// # Arguments
/// * `a` - First number
/// * `b` - Second number
///
/// # Examples
/// ```
/// let result = my_crate::add(2, 3);
/// assert_eq!(result, 5);
/// ```
///
/// # Panics
/// Never panics.
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### Generating Documentation

```bash
cargo doc --open  # Generate and open docs
```

---

## 3️⃣ Error Handling Strategies

### Using `anyhow` for Applications

```rust
use anyhow::{Result, Context};

fn read_config() -> Result<Config> {
    let content = std::fs::read_to_string("config.json")
        .context("Failed to read config")?;
    serde_json::from_str(&content)
        .context("Failed to parse config")
}

fn main() -> Result<()> {
    let config = read_config()?;
    println!("{:?}", config);
    Ok(())
}
```

### Using `thiserror` for Libraries

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
    
    #[error("Parse error: {0}")]
    Parse(#[from] serde_json::Error),
    
    #[error("Invalid input")]
    InvalidInput,
}
```

---

## 4️⃣ Performance Optimization

### Benchmarking

```bash
cargo bench  # Run benchmarks
```

### Optimization Tips

```rust
// 1. Use Vec::with_capacity when size is known
let mut v = Vec::with_capacity(100);  // Allocate once

// 2. Prefer iterators over manual loops
let sum: i32 = v.iter().sum();  // Optimizable
let mut sum = 0;
for x in &v { sum += x; }  // Less optimizable

// 3. Use references instead of cloning
fn process(data: &[i32]) {}  // Good
fn process(data: Vec<i32>) {}  // Bad - moved

// 4. Enable optimizations in release
// Cargo.toml:
// [profile.release]
// opt-level = 3
// lto = true
// codegen-units = 1
```

---

## 5️⃣ Production Patterns

### Configuration

```rust
use std::env;

pub struct Config {
    pub host: String,
    pub port: u16,
    pub database_url: String,
}

impl Config {
    pub fn from_env() -> Result<Self, ConfigError> {
        Ok(Config {
            host: env::var("HOST").unwrap_or("localhost".to_string()),
            port: env::var("PORT").unwrap_or("8080".to_string()).parse()?,
            database_url: env::var("DATABASE_URL")?,
        })
    }
}
```

### Logging

```rust
use tracing::{info, warn, error};

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt::init();
    
    info!("Application started");
    warn!("This is a warning");
    error!("This is an error");
}
```

### Graceful Shutdown

```rust
use tokio::signal;

#[tokio::main]
async fn main() {
    let shutdown = signal::ctrl_c();
    
    tokio::select! {
        _ = shutdown => {
            println!("Shutting down...");
            // Cleanup
        }
    }
}
```

---

## 🎓 Phase 7 Summary

✅ **Testing**: Unit, integration, and doc tests
✅ **Documentation**: Doc comments and rustdoc
✅ **Error Handling**: anyhow for apps, thiserror for libraries
✅ **Performance**: Benchmarking and optimization
✅ **Production**: Configuration, logging, graceful shutdown

---

# 🎉 CONGRATULATIONS! 🎉

## You've Completed the 2-Month Journey from Beginner to Advanced Rust Developer!

### What You've Mastered

| Phase | Topic | Skills |
|-------|-------|--------|
| 1 | Basics | Variables, types, control flow, functions |
| 2 | Ownership | Borrowing, references, lifetimes, smart pointers |
| 3 | Collections | Vec, HashMap, String, Result, Option |
| 4 | Type System | Traits, generics, associated types, trait objects |
| 5 | Advanced | Closures, iterators, async intro, builder pattern |
| 6 | Concurrency | Threads, channels, Mutex, Arc, async patterns |
| 7 | Production | Testing, docs, error handling, optimization |

### You Can Now Build

✅ Safe, fast systems programs
✅ Web servers and APIs
✅ Concurrent applications
✅ Command-line tools
✅ Data processing pipelines
✅ Production-grade software

### Continue Your Learning

**Explore Specializations:**
- **WebAssembly**: Compile Rust to WASM
- **Embedded**: Microcontroller programming
- **Blockchain**: Smart contracts
- **Game Development**: Bevy engine
- **CLI Tools**: Clap, structopt

**Resources:**
- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rust for Rustaceans](https://nostarch.com/rust-for-rustaceans)
- [Rustlings](https://github.com/rust-lang/rustlings)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)

**Community:**
- Reddit: r/rust
- Discord: Rust Community Discord
- GitHub: Contribute to open source

---

## Final Tips for Success

1. **Read Error Messages Carefully** - The Rust compiler is your teacher
2. **Practice Ownership** - It's the hardest part initially
3. **Use the Type System** - Let types guide you
4. **Write Tests** - Catch bugs early
5. **Read Others' Code** - Learn patterns
6. **Build Projects** - Apply knowledge to real problems
7. **Ask Questions** - Community is helpful and welcoming

---

**You're ready to write Rust in production. Trust the compiler, embrace the learning curve, and build amazing things!** 🦀🚀

*Happy Rusting!*

# 🦀 PHASE 6: ADVANCED RUST - COMPLETE DETAILED CHEAT SHEET

## Table of Contents

1. Macros (macro_rules!)
2. Derive Macros
3. Procedural Macros
4. Build Scripts
5. Cargo and Dependencies
6. Testing in Rust
7. Documentation (rustdoc)

---

## 1️⃣ MACROS (macro_rules!)

### 📖 What is it?

Declarative macros are Rust's most accessible metaprogramming tool. They operate on Rust code's abstract syntax tree (AST) and generate new code at compile time. Unlike functions, macros can accept variable numbers of arguments and operate on syntax patterns.

### 🎯 When to Use Macros

- **Reducing boilerplate**: When you find yourself writing the same code pattern repeatedly
- **Creating DSLs**: Building domain-specific languages for configuration, testing, or queries
- **Variadic functions**: Implementing functions that accept variable numbers of arguments (like println!)
- **Compile-time validation**: Checking syntax or semantics before code runs
- **Code generation**: Creating repetitive implementations like trait impls for multiple types
- **When functions can't**: Operations that need syntax-level transformations or access to caller context

### 💡 Why Use Macros

- **Zero-cost abstraction**: Macro expansion happens at compile time, no runtime overhead
- **Hygienic**: Macros respect scoping rules and won't accidentally capture variables
- **Pattern-based**: Easy to read and write with familiar pattern matching syntax
- **Flexible**: Can generate any valid Rust code, including functions, structs, and impls
- **Error reporting**: Can provide custom error messages at compile time

### 📝 Complete Methods Reference

#### Basic Macro Definition and Patterns

```rust
// Why macro: Create reusable code patterns without function overhead
// When: Code that needs to be expanded inline, like assertions or logging

// Simple macro with no arguments
macro_rules! say_hello {
    // Why pattern: Match the macro invocation syntax
    () => {
        println!("Hello!");
    };
}

fn basic_macro() {
    say_hello!(); // Expands to println!("Hello!");
}

// Macro with arguments - captures expression
macro_rules! greet {
    ($name:expr) => {
        println!("Hello, {}!", $name);
    };
}

fn macro_with_args() {
    greet!("Alice"); // Expands to println!("Hello, {}!", "Alice");
}

// Multiple patterns (overloading)
macro_rules! log {
    // Single argument - just message
    ($msg:expr) => {
        println!("[LOG] {}", $msg);
    };
    // With severity level
    ($level:expr, $msg:expr) => {
        println!("[{}] {}", $level, $msg);
    };
    // With format arguments
    ($($arg:tt)*) => {
        println!($($arg)*);
    };
}

fn multiple_patterns() {
    log!("Simple message");                    // [LOG] Simple message
    log!("INFO", "Informational");             // [INFO] Informational
    log!("Value: {}", 42);                     // Value: 42
}
```

#### Metavariable Types (What Macros Can Capture)

```rust
// Why different metavariables: Capture different parts of Rust syntax
// When: You need to generate code with specific syntax elements

macro_rules! capture_examples {
    // expr - expression (most common)
    ($e:expr) => {
        println!("Expression evaluates to: {}", $e);
    };
    
    // ident - identifier (variable names, function names)
    ($i:ident) => {
        println!("Identifier name: {}", stringify!($i));
        let $i = 42; // Can use as variable name
    };
    
    // ty - type
    ($t:ty) => {
        println!("Type: {}", stringify!($t));
        let _x: $t = Default::default();
    };
    
    // block - block of statements
    ($b:block) => {
        println!("Block returns: {}", $b);
    };
    
    // literal - literal values
    ($l:literal) => {
        println!("Literal: {}", $l);
    };
    
    // path - paths (e.g., std::vec::Vec)
    ($p:path) => {
        println!("Path: {}", stringify!($p));
        let _x: $p<i32> = $p::new();
    };
    
    // tt - token tree (any single token or group)
    ($($t:tt)*) => {
        println!("Token tree: {}", stringify!($($t)*));
    };
}

fn metavariable_types() {
    capture_examples!(42);              // expr
    capture_examples!(my_variable);     // ident (creates variable)
    capture_examples!(i32);             // ty
    capture_examples!({ 1 + 2 });       // block
    capture_examples!("hello");         // literal
    capture_examples!(std::vec::Vec);   // path
    capture_examples!(1 + 2 * 3);       // token tree
}
```

#### Repetition in Macros

```rust
// Why repetition: Handle variable numbers of arguments
// When: Implementing variadic functions or processing lists

// Basic repetition - zero or more times (*)
macro_rules! vec {
    ($($x:expr),*) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}

// With trailing comma support (optional separator)
macro_rules! my_vec {
    ($($x:expr),* $(,)?) => {
        {
            let mut v = Vec::new();
            $(
                v.push($x);
            )*
            v
        }
    };
}

// Multiple repetition groups
macro_rules! map {
    // Key-value pairs with => separator
    ($($key:expr => $value:expr),* $(,)?) => {
        {
            let mut map = std::collections::HashMap::new();
            $(
                map.insert($key, $value);
            )*
            map
        }
    };
}

fn repetition_example() {
    // Standard vec macro
    let v1 = vec![1, 2, 3, 4];
    
    // Custom vec with trailing comma support
    let v2 = my_vec![1, 2, 3,];
    
    // Map macro
    let map = map![
        "a" => 1,
        "b" => 2,
        "c" => 3,
    ];
    
    println!("Vector: {:?}, Map: {:?}", v2, map);
}

// Repetition separators
// *  - zero or more (with separator)
// +  - one or more (with separator)
// ?  - zero or one (optional)
macro_rules! optional {
    // Optional argument
    ($($arg:expr)?) => {
        {
            let value = $($arg)* 0;
            println!("Value: {}", value);
        }
    };
}
```

#### Recursive Macros

```rust
// Why recursion: Generate repetitive nested structures
// When: Creating expressions with variable depth, like sums or nested types

// Recursive sum macro
macro_rules! recursive_sum {
    // Base case: single value
    ($x:expr) => {
        $x
    };
    // Recursive case: sum of many values
    ($x:expr, $($rest:expr),*) => {
        $x + recursive_sum!($($rest),*)
    };
}

// Recursive macro with count
macro_rules! repeat {
    // Entry point
    ($count:expr, $value:expr) => {
        repeat!(@internal $count, $value, [])
    };
    // Base case - count reached 0
    (@internal 0, $value:expr, [$($result:expr),*]) => {
        [$($result),*]
    };
    // Recursive case
    (@internal $count:expr, $value:expr, [$($result:expr),*]) => {
        repeat!(@internal $count - 1, $value, [$($result,)* $value])
    };
}

// Recursive type generation
macro_rules! tuple {
    // Single element
    ($x:ident) => {
        ($x,)
    };
    // Multiple elements
    ($first:ident, $($rest:ident),*) => {
        ($first, tuple!($($rest),*))
    };
}

fn recursion_example() {
    let sum = recursive_sum!(1, 2, 3, 4, 5);
    println!("Sum: {}", sum); // 15
    
    let arr = repeat!(5, 42);
    println!("{:?}", arr); // [42, 42, 42, 42, 42]
    
    let t = tuple!(a, b, c);
    // Expands to (a, (b, (c,)))
}
```

### 🏆 Best Practices

```rust
// 1. Document macros thoroughly with examples
/// Creates a hashmap from key-value pairs.
///
/// # Examples
/// ```
/// let map = hashmap!{
///     "a" => 1,
///     "b" => 2,
/// };
/// assert_eq!(map.len(), 2);
/// ```
macro_rules! hashmap {
    ($($key:expr => $value:expr),* $(,)?) => { /* ... */ };
}

// 2. Use consistent naming (snake_case for macros, ! at end)
macro_rules! my_macro { }  // Good
macro_rules! myMacro { }   // Bad

// 3. Handle trailing commas gracefully for user convenience
macro_rules! vec {
    ($($x:expr),* $(,)?) => { /* ... */ };
}

// 4. Use $crate to refer to your crate's items
#[macro_export]
macro_rules! my_macro {
    () => {
        $crate::my_function()  // Refers to this crate's function
    };
}

// 5. Provide helpful error messages
macro_rules! require {
    ($cond:expr, $msg:expr) => {
        if !$cond {
            compile_error!($msg);  // Compile-time error
        }
    };
}

// 6. Keep macros simple - prefer functions when they can do the job
// ✅ Good: Function for simple operations
fn add(x: i32, y: i32) -> i32 { x + y }

// ❌ Bad: Macro for simple operations
macro_rules! add {
    ($x:expr, $y:expr) => { $x + $y };
}
```

---

## 2️⃣ DERIVE MACROS

### 📖 What is it?

Derive macros automatically implement traits for structs and enums using the `#[derive]` attribute. They're the most common form of procedural macros, allowing you to add custom behavior to your types with minimal boilerplate.

### 🎯 When to Use Derive Macros

- **Implementing common traits**: Debug, Clone, PartialEq, Default for your types
- **Custom serialization**: Auto-generate Serde's Serialize/Deserialize
- **Validation**: Generate validation logic based on field attributes
- **Builder pattern**: Automatically generate builder code for structs
- **ORM mapping**: Generate database mapping code for models
- **Testing**: Generate test data or mock implementations

### 💡 Why Use Derive Macros

- **Boilerplate reduction**: Automatically implement repetitive trait code
- **Consistency**: Same pattern across all types in your codebase
- **Productivity**: Focus on business logic, not repetitive implementations
- **Maintainability**: One source of truth for trait implementations
- **Type safety**: Generated code is checked by the compiler

### 📝 Complete Methods Reference

#### Standard Derivable Traits

```rust
// Why derive: Automatic implementation of common traits
// When: For data types that need standard behavior

// Debug - enables {:?} formatting (most important)
#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
}

// Clone - allows explicit copying (heap data is cloned, not just pointers)
#[derive(Clone)]
struct Config {
    host: String,
    port: u16,
}

// Copy - allows implicit copying (only for types where all fields are Copy)
#[derive(Copy, Clone)]  // Copy requires Clone
struct Coordinate {
    x: i32,
    y: i32,
}

// PartialEq - enables == and != comparisons
#[derive(PartialEq)]
struct Color {
    r: u8,
    g: u8,
    b: u8,
}

// Eq - for strict equality (requires PartialEq)
#[derive(PartialEq, Eq)]
struct Id(u32);

// PartialOrd - enables <, >, <=, >= comparisons
#[derive(PartialOrd, PartialEq)]
struct Score(u32);

// Ord - for total ordering (requires Eq + PartialOrd)
#[derive(Ord, PartialOrd, Eq, PartialEq)]
struct Priority(i32);

// Default - provides default values (uses Default for each field)
#[derive(Default)]
struct Settings {
    volume: u8,      // Default: 0
    brightness: u8,  // Default: 0
    theme: String,   // Default: ""
    enabled: bool,   // Default: false
}

// Hash - for use in HashMaps and HashSets
use std::hash::Hash;
#[derive(Hash, PartialEq, Eq)]
struct UserId(u64);

// Combined example - most common set
#[derive(Debug, Clone, PartialEq, Default)]
struct User {
    username: String,
    email: String,
    age: u32,
}

fn standard_derive_example() {
    let user1 = User::default();
    let user2 = user1.clone();
    
    println!("Debug: {:?}", user1);
    
    if user1 == user2 {
        println!("Users are equal!");
    }
}
```

#### Serde Serialization Derive

```rust
// Why serde: Automatically serialize/deserialize to many formats
// When: Building APIs, configuration files, data persistence

use serde::{Serialize, Deserialize};

// Basic serialization
#[derive(Serialize, Deserialize, Debug)]
struct Person {
    name: String,
    age: u32,
    email: Option<String>,
}

// Renaming fields
#[derive(Serialize, Deserialize)]
struct User {
    #[serde(rename = "user_name")]
    name: String,
    
    #[serde(rename = "user_age")]
    age: u32,
}

// Skipping fields
#[derive(Serialize)]
struct InternalData {
    public_field: String,
    #[serde(skip)]
    secret_field: String,
}

// Default values
#[derive(Deserialize)]
#[serde(default)]
struct Config {
    host: String,
    port: u16,
    timeout: u64,
}

impl Default for Config {
    fn default() -> Self {
        Config {
            host: "localhost".to_string(),
            port: 8080,
            timeout: 30,
        }
    }
}

// Flattening fields
#[derive(Serialize)]
struct Info {
    name: String,
    #[serde(flatten)]
    extra: std::collections::HashMap<String, String>,
}
```

### 🏆 Best Practices

```rust
// 1. Derive only what you actually need
#[derive(Debug)]  // Only Debug - minimal
struct Minimal;

// 2. Combine standard and custom derives
#[derive(Debug, Clone, Serialize, Deserialize, Builder)]
struct ComplexConfig {
    // Fields...
}

// 3. Document derived traits
/// A user in the system.
/// 
/// # Derives
/// - `Debug` - for logging and debugging
/// - `Clone` - for duplication in memory
/// - `PartialEq` - for comparison in tests
/// - `Serialize`/`Deserialize` - for API communication
#[derive(Debug, Clone, PartialEq, Serialize, Deserialize)]
struct DocumentedUser {
    // ...
}

// 4. Use serde attributes for customization
#[derive(Serialize, Deserialize)]
struct ApiResponse {
    #[serde(rename = "status_code")]
    code: u32,
    
    #[serde(skip_serializing_if = "Option::is_none")]
    data: Option<String>,
    
    #[serde(default)]
    version: String,
}
```

---

## 3️⃣ PROCEDURAL MACROS

### 📖 What is it?

Procedural macros are functions that operate on Rust code at compile time, allowing you to extend Rust's syntax. Unlike declarative macros (macro_rules!), they have full access to Rust's AST and can perform arbitrary code transformations.

### 🎯 When to Use Procedural Macros

- **Creating DSLs**: Custom syntax for specialized domains
- **Attribute macros**: Adding annotations to items (like `#[route(GET, "/")]`)
- **Function-like macros**: Custom syntax similar to `println!` but with complex parsing
- **Code generation**: Generating repetitive code based on type structure
- **Compile-time validation**: Checking constraints that can't be expressed in the type system

### 💡 Why Use Procedural Macros

- **Maximum flexibility**: Full access to Rust's AST (Abstract Syntax Tree)
- **Powerful transformations**: Can rewrite code arbitrarily
- **Domain-specific extensions**: Create new syntax for your problem domain
- **Compile-time guarantees**: Validate invariants before code runs
- **Integration with IDEs**: Good support for macros in modern IDEs

### 📝 Complete Methods Reference

#### Types of Procedural Macros

```rust
// In Cargo.toml
// [lib]
// proc-macro = true

use proc_macro::TokenStream;
use quote::quote;
use syn::{parse_macro_input, DeriveInput, ItemFn, LitStr};

// 1. Function-like macros (like vec!, println!)
#[proc_macro]
pub fn my_macro(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as syn::Expr);
    
    let expanded = quote! {
        println!("Macro called with: {}", #input);
        #input
    };
    
    TokenStream::from(expanded)
}

// 2. Attribute macros (like #[derive], #[test])
#[proc_macro_attribute]
pub fn log_execution(args: TokenStream, input: TokenStream) -> TokenStream {
    let input_fn = parse_macro_input!(input as ItemFn);
    let fn_name = &input_fn.sig.ident;
    let fn_block = &input_fn.block;
    let inputs = &input_fn.sig.inputs;
    let output = &input_fn.sig.output;
    
    let expanded = quote! {
        fn #fn_name(#inputs) #output {
            println!("Entering: {}", stringify!(#fn_name));
            let start = std::time::Instant::now();
            let result = #fn_block;
            let duration = start.elapsed();
            println!("Exiting: {} (took {:?})", stringify!(#fn_name), duration);
            result
        }
    };
    
    TokenStream::from(expanded)
}

// 3. Derive macros (covered in previous section)
#[proc_macro_derive(MyTrait)]
pub fn derive_my_trait(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as DeriveInput);
    // Implementation...
    TokenStream::new()
}
```

#### Attribute Macro Example: HTTP Routing

```rust
// Why route macro: Define API routes declaratively
// When: Building web servers, creating REST APIs

#[proc_macro_attribute]
pub fn route(args: TokenStream, input: TokenStream) -> TokenStream {
    // Parse route arguments: #[route(GET, "/users")]
    let args = syn::parse_macro_input!(args as syn::AttributeArgs);
    let method = args.get(0).expect("HTTP method required");
    let path = args.get(1).expect("Route path required");
    
    let input_fn = parse_macro_input!(input as ItemFn);
    let fn_name = &input_fn.sig.ident;
    let fn_block = &input_fn.block;
    
    let method_str = match method {
        syn::NestedMeta::Lit(syn::Lit::Str(s)) => s.value(),
        _ => panic!("Method must be a string literal"),
    };
    
    let path_str = match path {
        syn::NestedMeta::Lit(syn::Lit::Str(s)) => s.value(),
        _ => panic!("Path must be a string literal"),
    };
    
    let expanded = quote! {
        #[allow(non_snake_case)]
        fn #fn_name() -> String {
            #fn_block
        }
        
        // Register route at startup
        router.add_route(#method_str, #path_str, #fn_name);
    };
    
    TokenStream::from(expanded)
}

// Usage
#[route(GET, "/")]
fn index() -> String {
    "Hello, World!".to_string()
}

#[route(POST, "/users")]
fn create_user() -> String {
    "User created".to_string()
}
```

### 🏆 Best Practices

```rust
// 1. Validate inputs thoroughly and provide helpful errors
#[proc_macro]
pub fn safe_macro(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as syn::Expr);
    
    if !valid {
        return syn::Error::new(Span::call_site(), "Invalid input")
            .to_compile_error()
            .into();
    }
    
    // Continue...
}

// 2. Use quote! for readable code generation
let expanded = quote! {
    // Generated code is readable and maintainable
    impl MyTrait for #name {
        fn method(&self) -> i32 {
            42
        }
    }
};

// 3. Keep macros in separate crates for better organization
// Cargo.toml
// [lib]
// proc-macro = true

// 4. Document macros thoroughly with examples
/// Adds logging to a function.
///
/// # Examples
/// ```
/// #[log_execution]
/// fn add(a: i32, b: i32) -> i32 {
///     a + b
/// }
/// ```
#[proc_macro_attribute]
pub fn log_execution(args: TokenStream, input: TokenStream) -> TokenStream { }
```

---

## 4️⃣ BUILD SCRIPTS

### 📖 What is it?

Build scripts (`build.rs`) are Rust files that run at compile time before the rest of the crate is compiled. They can generate code, set environment variables, and handle native dependencies.

### 🎯 When to Use Build Scripts

- **Code generation**: Generating Rust code from external files (JSON, protobuf, etc.)
- **Native dependencies**: Compiling and linking C/C++ libraries
- **Version information**: Embedding git commit hashes, build timestamps
- **Conditional compilation**: Setting cfg flags based on environment or platform
- **Resource embedding**: Including static assets in the binary
- **Platform detection**: Adjusting build based on target OS or architecture

### 💡 Why Use Build Scripts

- **Flexibility**: Generate code at compile time based on external data
- **Portability**: Handle platform-specific code and dependencies
- **Integration**: Seamlessly work with native libraries and system tools
- **Automation**: Eliminate manual steps in the build process
- **Reproducibility**: Capture build-time information in the binary

### 📝 Complete Methods Reference

#### Basic Build Script Structure

```rust
// build.rs
use std::env;
use std::fs;
use std::path::Path;

fn main() {
    // Tell Cargo when to rerun the build script
    println!("cargo:rerun-if-changed=src/schema.json");
    println!("cargo:rerun-if-changed=build.rs");
    
    // Read external file
    let schema = fs::read_to_string("src/schema.json").unwrap();
    
    // Generate Rust code
    let output = format!(
        r#"
        /// Generated schema from build script
        pub const SCHEMA: &str = r#"{}"#;
        "#,
        schema
    );
    
    // Write to OUT_DIR
    let out_dir = env::var("OUT_DIR").unwrap();
    let dest_path = Path::new(&out_dir).join("schema.rs");
    fs::write(dest_path, output).unwrap();
    
    // Notify Cargo about generated files
    println!("cargo:rustc-env=SCHEMA_PATH={}", dest_path.display());
}
```

#### Environment Variables and Git Information

```rust
// build.rs - Embed git information
use std::env;
use std::process::Command;

fn main() {
    // Get git commit hash
    let commit_hash = env::var("GIT_COMMIT")
        .unwrap_or_else(|_| {
            let output = Command::new("git")
                .args(["rev-parse", "HEAD"])
                .output()
                .ok();
            
            output
                .and_then(|o| String::from_utf8(o.stdout).ok())
                .unwrap_or_else(|| "unknown".to_string())
                .trim()
                .to_string()
        });
    
    // Get git branch
    let branch = Command::new("git")
        .args(["rev-parse", "--abbrev-ref", "HEAD"])
        .output()
        .ok()
        .and_then(|o| String::from_utf8(o.stdout).ok())
        .unwrap_or_else(|| "unknown".to_string())
        .trim()
        .to_string();
    
    // Set environment variables
    println!("cargo:rustc-env=GIT_COMMIT_HASH={}", commit_hash);
    println!("cargo:rustc-env=GIT_BRANCH={}", branch);
    
    // For conditional compilation
    if !commit_hash.contains("dirty") {
        println!("cargo:rustc-cfg=clean_repo");
    }
    
    println!("cargo:rerun-if-changed=.git/HEAD");
}
```

---

## 5️⃣ CARGO AND DEPENDENCIES

### 📖 What is it?

Cargo is Rust's build system and package manager. It handles dependency management, building, testing, and publishing crates to crates.io.

### 🎯 When to Use Cargo Features

- **Managing dependencies**: Adding, updating, and removing crates
- **Feature flags**: Conditionally compiling parts of your code
- **Workspaces**: Managing multiple crates in a single repository
- **Publishing**: Sharing your crates on crates.io
- **Custom commands**: Extending Cargo with custom functionality

### 💡 Why Use Cargo

- **Standardization**: One tool for all Rust projects
- **Dependency management**: Automatic resolution and updates
- **Feature flags**: Fine-grained control over what's included
- **Workspaces**: Efficient multi-crate project management
- **Built-in commands**: cargo build, cargo test, cargo doc, cargo publish

### 📝 Complete Methods Reference

#### Cargo.toml Structure

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"
authors = ["Your Name <email@example.com>"]
description = "A description of your project"
license = "MIT OR Apache-2.0"
repository = "https://github.com/username/my_project"
documentation = "https://docs.rs/my_project"
readme = "README.md"
keywords = ["rust", "example", "tutorial"]
categories = ["development-tools", "web-programming"]

[dependencies]
# Version requirements
serde = "1.0"                    # Any 1.0.x version
tokio = { version = "1.0", features = ["full"] }  # With features
regex = "1.5.1"                  # Exact version

# Git dependencies
my_crate = { git = "https://github.com/user/repo.git", branch = "main" }

# Path dependencies (local)
my_local = { path = "../my_local" }

# Optional dependencies
json = { version = "0.12", optional = true }

[dev-dependencies]
# Dependencies only for testing
assert_matches = "1.5"
tempfile = "3.3"

[build-dependencies]
# Dependencies only for build script
anyhow = "1.0"

[features]
# Feature flags
default = ["std"]                # Default features
std = []                         # No dependencies
serde_support = ["serde", "serde_json"]  # Enable with dependencies
async = ["tokio"]
full = ["serde_support", "async", "std"]

[profile.release]
opt-level = 3                    # Optimize for speed
lto = true                       # Link-time optimization
codegen-units = 1                # Better optimization
```

#### Feature Flags Implementation

```rust
// src/lib.rs
// Why features: Conditional compilation for optional functionality
// When: You want users to choose what to include

// Conditional compilation based on features
#[cfg(feature = "std")]
use std::collections::HashMap;

#[cfg(not(feature = "std"))]
use alloc::collections::BTreeMap;

// Feature-gated module
#[cfg(feature = "serde_support")]
pub mod serialization {
    use serde::{Serialize, Deserialize};
    
    #[derive(Serialize, Deserialize)]
    pub struct Config {
        // ...
    }
}

// Feature-gated function
#[cfg(feature = "async")]
pub async fn async_operation() -> Result<(), Box<dyn std::error::Error>> {
    // Async implementation
    Ok(())
}
```

### 🏆 Best Practices

```toml
# 1. Use semantic versioning
version = "0.1.0"  # Initial development
version = "1.0.0"  # Stable API

# 2. Document features
[features]
# Enable serialization support using serde
serde = ["dep:serde", "serde/derive"]
# Enable async runtime features
async = ["tokio", "futures"]
# Full feature set
full = ["serde", "async", "std"]

# 3. Use workspace inheritance
[workspace.package]
version = "0.1.0"
edition = "2021"
authors = ["Your Name <email@example.com>"]

# 4. Specify minimal dependency versions
serde = { version = "1.0", default-features = false }
```

---

## 6️⃣ TESTING IN RUST

### 📖 What is it?

Rust has first-class testing support with a built-in test harness, rich assertion macros, and documentation tests. Testing is integrated directly into the language and toolchain.

### 🎯 When to Use Testing

- **Unit tests**: Testing individual functions and modules
- **Integration tests**: Testing public APIs and external interfaces
- **Doc tests**: Ensuring documentation examples are correct
- **Benchmarking**: Measuring performance and finding bottlenecks
- **Property-based testing**: Testing with automatically generated inputs
- **Mocking**: Isolating components for testing

### 💡 Why Test in Rust

- **Built-in**: No external frameworks needed for basic testing
- **Parallel**: Tests run in parallel by default (faster test runs)
- **Doc tests**: Documentation examples stay correct over time
- **Rich assertions**: `assert_eq!`, `assert_ne!`, `assert!`, custom messages
- **Integration**: Seamless integration with Cargo

### 📝 Complete Methods Reference

#### Unit Tests

```rust
// src/lib.rs
// Why unit tests: Test individual components in isolation
// When: Testing functions, structs, and modules

pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

pub fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

#[cfg(test)]
mod tests {
    use super::*;
    
    // Basic test
    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
        assert_ne!(add(2, 2), 5);
        assert!(add(1, 1) == 2);
    }
    
    // Test with custom message
    #[test]
    fn test_add_with_message() {
        let result = add(2, 3);
        assert_eq!(result, 5, "Expected 5, got {}", result);
    }
    
    // Test returning Result
    #[test]
    fn test_divide() -> Result<(), String> {
        let result = divide(10.0, 2.0)?;
        assert_eq!(result, 5.0);
        Ok(())
    }
    
    // Test with assert matches
    #[test]
    fn test_divide_by_zero() {
        let result = divide(10.0, 0.0);
        assert!(result.is_err());
        assert_eq!(result.unwrap_err(), "Division by zero");
    }
    
    // Test with should_panic
    #[test]
    #[should_panic(expected = "attempt to multiply with overflow")]
    fn test_overflow() {
        let _ = i32::MAX * 2;
    }
    
    // Ignored test (expensive)
    #[test]
    #[ignore]
    fn expensive_test() {
        // Run with: cargo test -- --ignored
    }
    
    // Multiple assertions
    #[test]
    fn test_multiple_assertions() {
        let result = add(2, 3);
        assert_eq!(result, 5);
        assert!(result > 0);
        assert!(result < 10);
    }
}
```

#### Integration Tests

```rust
// tests/integration_test.rs
// Why integration tests: Test public API from consumer perspective
// When: Testing library behavior as a whole

use my_lib;

#[test]
fn test_from_integration() {
    let result = my_lib::add(2, 3);
    assert_eq!(result, 5);
}

#[test]
fn test_error_handling() {
    let result = my_lib::divide(10.0, 0.0);
    assert!(result.is_err());
}
```

#### Documentation Tests (Doc Tests)

```rust
// src/lib.rs
// Why doc tests: Ensure documentation examples are correct
// When: Writing public API documentation

/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// use my_lib::add;
///
/// let result = add(2, 3);
/// assert_eq!(result, 5);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

/// Divides two numbers.
///
/// # Examples
///
/// Basic division:
/// ```
/// # use my_lib::divide;
/// let result = divide(10.0, 2.0).unwrap();
/// assert_eq!(result, 5.0);
/// ```
///
/// # Errors
/// Returns `Err` if the divisor is zero.
pub fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}
```

### 🏆 Best Practices

```rust
// 1. Use descriptive test names
#[test]
fn test_add_positive_numbers_returns_sum() { }

// 2. Arrange-Act-Assert pattern
#[test]
fn test_pattern() {
    // Arrange
    let a = 2;
    let b = 3;
    
    // Act
    let result = add(a, b);
    
    // Assert
    assert_eq!(result, 5);
}

// 3. Use test modules for organization
#[cfg(test)]
mod tests {
    use super::*;
    
    mod unit_tests {
        use super::*;
        // Unit tests
    }
    
    mod integration_tests {
        use super::*;
        // Integration tests
    }
}

// 4. Keep tests deterministic
#[test]
fn test_deterministic() {
    // Use fixed inputs, not random values
    let input = 42;
    let result = process(input);
    assert_eq!(result, 84);
}

// 5. Test edge cases
#[test]
fn test_empty_input() { }
#[test]
fn test_maximum_values() { }
#[test]
fn test_minimum_values() { }
```

---

## 7️⃣ DOCUMENTATION (RUSTDOC)

### 📖 What is it?

rustdoc generates beautiful HTML documentation from code comments. It's integrated with Cargo and produces searchable, linked documentation with examples and cross-references.

### 🎯 When to Write Documentation

- **Public API**: Every public item needs documentation
- **Examples**: Show how to use the API with runnable examples
- **Tutorials**: Longer guides for complex features or workflows
- **README**: Project overview, installation, quick start
- **Contribution guides**: How to contribute to the project

### 💡 Why Document

- **Usability**: Users understand how to use your code without reading source
- **Maintainability**: Future maintainers understand design decisions
- **Discovery**: Searchable documentation on docs.rs
- **Professionalism**: Well-documented crates are trusted more
- **Examples**: Documentation examples serve as tests

### 📝 Complete Methods Reference

#### Basic Documentation

```rust
/// A point in 2D space.
///
/// This struct represents a point with x and y coordinates.
/// It provides basic operations like distance calculation.
pub struct Point {
    /// The x coordinate.
    pub x: f64,
    /// The y coordinate.
    pub y: f64,
}

impl Point {
    /// Creates a new point.
    ///
    /// # Arguments
    ///
    /// * `x` - The x coordinate
    /// * `y` - The y coordinate
    ///
    /// # Examples
    ///
    /// ```
    /// use my_lib::Point;
    ///
    /// let point = Point::new(1.0, 2.0);
    /// assert_eq!(point.x, 1.0);
    /// assert_eq!(point.y, 2.0);
    /// ```
    pub fn new(x: f64, y: f64) -> Self {
        Point { x, y }
    }
    
    /// Returns the distance from the origin (0, 0).
    ///
    /// This uses the Euclidean distance formula: sqrt(x² + y²)
    ///
    /// # Examples
    ///
    /// ```
    /// # use my_lib::Point;
    /// let point = Point::new(3.0, 4.0);
    /// assert_eq!(point.distance_from_origin(), 5.0);
    /// ```
    pub fn distance_from_origin(&self) -> f64 {
        (self.x * self.x + self.y * self.y).sqrt()
    }
}
```

#### Module Documentation

```rust
//! # My Library
//!
//! A library for doing amazing things with strings and numbers.
//!
//! ## Overview
//!
//! This library provides tools for:
//! * String manipulation
//! * Number processing
//! * Data validation
//!
//! ## Quick Start
//!
//! ```rust
//! use my_lib::{process_string, calculate};
//!
//! let processed = process_string("hello");
//! let result = calculate(2, 3);
//! assert_eq!(result, 5);
//! ```
//!
//! ## Feature Flags
//!
//! - `serde`: Enable serialization support
//! - `async`: Enable async operations
//! - `full`: Enable all features

pub mod string_utils;
pub mod math;
pub mod validation;
```

### 🏆 Best Practices

```rust
// 1. Document every public item
/// Public API needs documentation
pub fn public_function() { }

// 2. Include examples for complex APIs
/// # Examples
/// ```
/// let result = complex_function(42).unwrap();
/// assert!(result.is_positive());
/// ```
pub fn complex_function(value: i32) -> Result<i32, Error> { }

// 3. Use proper grammar and punctuation
/// This function does something important. It should be
/// described in complete sentences with proper punctuation.

// 4. Link to relevant items
/// See also [`other_function`] and [`struct::method`].

// 5. Document errors thoroughly
/// # Errors
/// Returns `Err` if:
/// - The input is empty
/// - The input exceeds maximum length
/// - The input contains invalid characters

// 6. Use `#![deny(missing_docs)]` in library crates
#![deny(missing_docs)]
```

---

## 📊 PHASE 6 COMPLETE - DECISION MATRIX

| Scenario | Macro Type | When to Use |
|----------|---|---|
| Reducing boilerplate | Declarative (macro_rules!) | Simple repetitive patterns |
| Custom derives | Derive macros | Automatic trait implementations |
| Attribute annotations | Attribute macros | Adding metadata to items |
| Custom syntax | Function-like macros | Creating DSLs |
| Compile-time code gen | Build scripts | Generating code from external data |
| Testing | Unit tests | Testing individual functions |
| API testing | Integration tests | Testing public interfaces |
| Example correctness | Doc tests | Ensuring examples work |
| Performance | Benchmarks | Measuring and optimizing |

---

## 🎯 PHASE 6 COMPLETE - ACHIEVEMENT SUMMARY

### What You've Achieved

| Topic | Skills Acquired |
|-------|---|
| **Macros** | Write declarative macros, pattern matching, repetition, DSLs |
| **Derive Macros** | Create custom derives, use syn and quote, parse attributes |
| **Procedural Macros** | Function-like and attribute macros, AST manipulation |
| **Build Scripts** | Code generation, environment variables, native libs |
| **Cargo** | Dependencies, features, workspaces, publishing |
| **Testing** | Unit tests, integration tests, doc tests, benchmarks, property tests |
| **Documentation** | rustdoc, examples, module docs, API docs |

### 🚀 What You Can Build

- **Macro-based DSLs** for configuration, queries, or testing
- **Custom derives** for automatic trait implementations
- **Attribute macros** for framework development (like `#[route]`)
- **Build-time code generation** from external data sources
- **Published crates** with comprehensive documentation
- **Test suites** with unit, integration, and property tests
- **Professional documentation** with examples and cross-references
- **Web frameworks** with attribute-based routing
- **ORM systems** with automatic database mappings
- **Code generators** from schemas and specifications

### 💡 Core Concepts Mastered

#### 1. Metaprogramming
- Declarative macros understand syntax patterns and AST
- Procedural macros have full control over code generation
- Build scripts enable compile-time code generation
- Macros reduce boilerplate while maintaining safety

#### 2. Trait Implementation Automation
- Derive macros automatically implement common traits
- Custom derives enable domain-specific implementations
- Serde demonstrates automatic serialization
- Builders and validators can be automatically generated

#### 3. Build System Integration
- Build scripts compile before main crate
- Environment variables configure compilation
- Platform detection enables conditional code
- Native libraries can be seamlessly integrated

#### 4. Comprehensive Testing Strategy
- Unit tests verify individual components
- Integration tests validate public APIs
- Doc tests ensure examples stay correct
- Property tests verify invariants across inputs
- Benchmarks track performance over time

#### 5. Documentation as First-Class Citizen
- rustdoc generates searchable HTML documentation
- Examples in docs serve as both documentation and tests
- Module-level docs explain architecture
- API documentation is generated from comments

#### 6. Cargo Ecosystem
- Features enable conditional compilation
- Workspaces organize multi-crate projects
- Publishing to crates.io shares code globally
- Dependency management prevents version conflicts

### 🎓 Real-World Applications

#### 1. Web Framework Development
```
Use attribute macros: #[get("/users/{id}")]
Use proc macros: Auto-generate route handlers
Use testing: Integration tests for endpoints
```

#### 2. ORM System
```
Use derive macros: #[derive(Model)]
Use build scripts: Generate schema from database
Use testing: Property tests for query generation
```

#### 3. Configuration Management
```
Use macros: DSL for config declaration
Use doc tests: Ensure config examples work
Use features: Conditional config sections
```

#### 4. API Client Library
```
Use procedural macros: #[endpoint]
Use doc tests: Ensure examples stay working
Use benchmarks: Track request performance
```

#### 5. Game Engine
```
Use macros: #[system] for ECS systems
Use build scripts: Asset preprocessing
Use testing: Property tests for physics
Use benchmarks: Frame time profiling
```

### ⚖️ Mastery Indicators

You've mastered Phase 6 when you can:

- [ ] Write declarative macros with complex patterns and recursion
- [ ] Create custom derive macros using syn and quote
- [ ] Build attribute macros for framework development
- [ ] Write build scripts that generate code from external sources
- [ ] Use Cargo features for conditional compilation
- [ ] Build comprehensive test suites (unit, integration, property)
- [ ] Write documentation that generates clean HTML with examples
- [ ] Publish crates to crates.io with proper metadata
- [ ] Debug macro expansion using `cargo expand`
- [ ] Understand when to use each metaprogramming technique
- [ ] Design ergonomic APIs using macros and derives
- [ ] Create documentation that serves as both reference and tests

### 🎉 Congratulations! You've Mastered Phase 6! 🎉

You now have **professional-level Rust skills** for:

- **Metaprogramming** with macros and procedural macros
- **Build automation** with build scripts
- **Project management** with Cargo
- **Quality assurance** with comprehensive testing
- **Documentation** with rustdoc

**Your Rust Journey Complete:**
- **Phase 1**: Language fundamentals and memory safety ✅
- **Phase 2**: Ownership and type system ✅
- **Phase 3**: Data structures and error handling ✅
- **Phase 4**: Abstractions with traits and generics ✅
- **Phase 5**: Concurrency and async programming ✅
- **Phase 6**: Advanced Rust and professional tools ✅

You've progressed from **beginner** learning basic syntax to **professional developer** capable of building sophisticated, production-ready systems.

**Next Steps:**
1. **Contribute to open source** - Apply skills to real projects
2. **Build specialized projects** - Web servers, CLI tools, game engines, etc.
3. **Deepen expertise** - Choose a specialization (async, systems, embedded, etc.)
4. **Share knowledge** - Write blogs, create tutorials, mentor others
5. **Explore ecosystem** - Use popular frameworks and libraries
6. **Stay current** - Follow Rust RFCs and language evolution

You're now equipped to build anything in Rust. The comprehensive toolkit you've learned applies across all domains and use cases. Whether you're building web services, systems software, game engines, or data processing systems, you have the knowledge and patterns to succeed.

**Welcome to the Rust community!** 🦀

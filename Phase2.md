# 🦀 PHASE 2: OWNERSHIP AND MEMORY SYSTEM - COMPLETE CHEAT SHEET

## 📚 Phase 2 Topics Overview

1. Ownership System
2. References and Borrowing
3. Mutable vs Immutable References
4. Lifetimes
5. Smart Pointers (Box, Rc, Arc, RefCell)

---

## 1️⃣ OWNERSHIP SYSTEM

### Syntax Structure

```rust
// Basic ownership
let owner = value;           // owner owns the value
let new_owner = owner;       // ownership MOVES (for non-Copy types)

// Cloning (deep copy)
let cloned = original.clone();  // Explicit deep copy

// Copy types (implicit copy)
let x = 5;
let y = x;                   // x is COPIED, still valid

// Functions and ownership
fn take_ownership(s: String) { }     // Takes ownership
fn borrow_reference(s: &String) { }  // Borrows (no ownership)
fn return_ownership() -> String { }  // Returns ownership
```

### Important Rules

| Rule | Description |
|------|-------------|
| One Owner | Each value has exactly one owner at a time |
| Move Semantics | Assigning transfers ownership (for non-Copy types) |
| Drop | When owner goes out of scope, value is dropped (freed) |
| Copy Types | Simple types (integers, floats, bool, char) implement Copy |
| Clone | Explicit deep copy for heap types |

### Copy vs Move Types

| Copy Types (Implicit Copy) | Move Types (Ownership Transfers) |
|---------------------------|----------------------------------|
| All integers (i32, u64, etc.) | String |
| All floats (f32, f64) | Vec<T> |
| bool | Box<T> |
| char | Custom structs (without Copy) |
| Tuples of Copy types | Rc<T>, Arc<T> |

### Memory Layout

```rust
// Stack-only (Copy)
let x = 5;
let y = x;  // Copies the actual value

// Heap + Stack (Move)
let s1 = String::from("hello");
let s2 = s1;  // Moves the pointer, NOT the heap data
// s1 is now invalid

// After clone
let s3 = s2.clone();  // Deep copy: new heap allocation
```

---

## 2️⃣ REFERENCES AND BORROWING

### Syntax Structure

```rust
// Immutable reference
let immut_ref = &value;           // &T
fn read_data(data: &String) { }   // Function parameter

// Mutable reference
let mut_ref = &mut value;         // &mut T
fn modify_data(data: &mut String) { }

// Dereferencing
let value = *reference;           // Explicit dereference
*ref = new_value;                 // Modify through mutable reference

// Slices (borrowed views)
let slice = &array[start..end];   // Array slice
let slice = &string[start..end];  // String slice (&str)
```

### Borrowing Rules

| Rule | Example |
|------|---------|
| Many immutable OR one mutable | `let r1 = &x; let r2 = &x;` ✅<br>`let r3 = &mut x;` ❌ (with r1/r2 alive) |
| References must be valid | `&x` cannot outlive x |
| No mutation while borrowed | Cannot modify data with active immutable references |

### Reference Scope (Non-Lexical Lifetimes)

```rust
fn main() {
    let mut data = 5;
    
    let r1 = &data;
    let r2 = &data;
    println!("{} {}", r1, r2);  // Last use of r1, r2
    
    // r1 and r2 are now "dead" — borrow is over
    let r3 = &mut data;  // ✅ Allowed!
    *r3 = 10;
}
```

---

## 3️⃣ MUTABLE VS IMMUTABLE REFERENCES

### Syntax Structure

```rust
// Immutable reference
let immut: &T = &value;

// Mutable reference
let mut_val: &mut T = &mut value;

// Function signatures
fn read(&self) -> &T { }           // Returns immutable reference
fn write(&mut self) { }            // Takes mutable reference
fn get(&self) -> &str { }          // Returns borrowed data
```

### Comparison Table

| Feature | &T (Immutable) | &mut T (Mutable) |
|---------|----------------|-----------------|
| Can read | ✅ Yes | ✅ Yes |
| Can modify | ❌ No | ✅ Yes |
| Multiple references | ✅ Many allowed | ❌ Only one |
| Can coexist with mutable | ❌ No | ❌ No |
| Use case | Reading, sharing | Modifying, exclusive access |

### Common Patterns

```rust
// Reading only
fn print_length(s: &String) {
    println!("{}", s.len());
}

// Modifying in place
fn add_world(s: &mut String) {
    s.push_str(" world");
}

// Returning borrowed data
fn first_char(s: &str) -> Option<char> {
    s.chars().next()
}

// Reborrowing
fn process(vec: &mut Vec<i32>) {
    vec.push(10);
}

fn main() {
    let mut data = vec![1, 2, 3];
    process(&mut data);  // Mutable reference reborrowed
    data.push(20);       // Still valid after function
}
```

---

## 4️⃣ LIFETIMES

### Syntax Structure

```rust
// Lifetime annotation
&'a T                    // Reference with lifetime 'a
&'a mut T                // Mutable reference with lifetime 'a

// Function with lifetimes
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct with lifetimes
struct Excerpt<'a> {
    part: &'a str,
}

// Multiple lifetimes
fn separate<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    x
}

// Lifetime bounds
fn example<'a, 'b: 'a>(x: &'a str, y: &'b str) -> &'a str {
    x  // 'b must outlive 'a
}
```

### Lifetime Elision Rules

| Rule | Input | Output (Elided) |
|------|-------|-----------------|
| 1 | `fn func(x: &i32)` | `fn func<'a>(x: &'a i32)` |
| 2 | `fn func(x: &i32) -> &i32` | `fn func<'a>(x: &'a i32) -> &'a i32` |
| 3 | `fn func(&self, x: &i32) -> &i32` | `fn func<'a, 'b>(&'a self, x: &'b i32) -> &'a i32` |

### When Annotations Are Required

```rust
// ✅ No annotations needed (elision applies)
fn first_word(s: &str) -> &str {
    s.split_whitespace().next().unwrap()
}

// ❌ Annotations required (multiple inputs, returns one)
fn longest(x: &str, y: &str) -> &str {  // Won't compile!
    if x.len() > y.len() { x } else { y }
}

// ✅ Fixed with annotations
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

### Static Lifetime ('static)

```rust
// String literal (lives for entire program)
let s: &'static str = "I live forever";

// Thread spawn requires 'static
use std::thread;

fn main() {
    let data = vec![1, 2, 3];
    thread::spawn(move || {
        println!("{:?}", data);
    });  // move ensures 'static
}
```

---

## 5️⃣ SMART POINTERS

### Syntax Structure

```rust
// Box<T> - Heap allocation
let boxed = Box::new(value);

// Rc<T> - Reference counting (single-threaded)
use std::rc::Rc;
let rc = Rc::new(value);
let clone = Rc::clone(&rc);  // Increase count
let count = Rc::strong_count(&rc);

// Arc<T> - Atomic reference counting (multi-threaded)
use std::sync::Arc;
let arc = Arc::new(value);
let clone = Arc::clone(&arc);

// RefCell<T> - Interior mutability
use std::cell::RefCell;
let cell = RefCell::new(value);
let borrow = cell.borrow();      // Immutable borrow (runtime)
let mut_borrow = cell.borrow_mut();  // Mutable borrow (runtime)

// Mutex<T> - Thread-safe interior mutability
use std::sync::Mutex;
let mutex = Mutex::new(value);
let guard = mutex.lock().unwrap();
*guard = new_value;
```

### Smart Pointer Comparison

| Smart Pointer | Ownership | Mutability | Thread-Safe | Use Case |
|---------------|-----------|-----------|-------------|----------|
| Box<T> | Single | Via &mut | Yes | Heap allocation, recursive types |
| Rc<T> | Multiple (shared) | Immutable | ❌ No | Single-threaded shared ownership |
| Arc<T> | Multiple (shared) | Immutable | ✅ Yes | Multi-threaded shared ownership |
| RefCell<T> | Single | Runtime checks | ❌ No | Interior mutability (single-threaded) |
| Mutex<T> | Single | Runtime checks | ✅ Yes | Interior mutability (multi-threaded) |

### Common Combinations

```rust
use std::rc::Rc;
use std::cell::RefCell;
use std::sync::{Arc, Mutex};

// Multiple owners with mutation (single-threaded)
type SharedData = Rc<RefCell<Vec<String>>>;
let data = Rc::new(RefCell::new(vec![]));
let clone = Rc::clone(&data);
clone.borrow_mut().push(String::from("item"));

// Multiple owners with mutation (multi-threaded)
type ThreadSafeData = Arc<Mutex<Vec<String>>>;
let data = Arc::new(Mutex::new(vec![]));
let clone = Arc::clone(&data);
let mut guard = clone.lock().unwrap();
guard.push(String::from("item"));
```

### Deref and Drop Traits

```rust
use std::ops::Deref;

// Deref allows smart pointers to act like references
struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;
    
    fn deref(&self) -> &T {
        &self.0
    }
}

// Drop runs when value goes out of scope
impl<T> Drop for MyBox<T> {
    fn drop(&mut self) {
        println!("Dropping MyBox");
    }
}
```

---

## 🎯 CRITICAL RULES TO MEMORIZE

### Ownership Rules
1. Each value has one owner
2. Only one owner at a time
3. Value is dropped when owner goes out of scope

### Borrowing Rules
1. Many immutable (`&T`) OR one mutable (`&mut T`) reference
2. References must never outlive the data they point to
3. No mutation while immutable borrows exist

### Lifetime Rules
1. Every reference has a lifetime
2. Lifetimes are usually elided (inferred)
3. Annotate when: multiple inputs, returns one reference
4. `'static` = lives for entire program

### Smart Pointer Rules
1. `Box<T>`: Use for heap allocation, recursive types
2. `Rc<T>`: Use for shared ownership (single-threaded)
3. `Arc<T>`: Use for shared ownership (multi-threaded)
4. `RefCell<T>`: Use for interior mutability (runtime checks)
5. Always use `Rc::clone()` not `.clone()` for reference counting

---

## 📝 QUICK REFERENCE CARD

### Common Patterns

```rust
// 1. Pass by reference (read-only)
fn process(data: &String) { }

// 2. Pass by mutable reference (modify)
fn modify(data: &mut String) { }

// 3. Return borrowed data (needs lifetimes)
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str { }

// 4. Heap allocation with Box
let boxed = Box::new(expensive_data);

// 5. Shared ownership (single-threaded)
let shared = Rc::new(data);
let clone = Rc::clone(&shared);

// 6. Shared ownership with mutation (single-threaded)
let shared = Rc::new(RefCell::new(data));
shared.borrow_mut().push(value);

// 7. Thread-safe shared data
let shared = Arc::new(Mutex::new(data));
let clone = Arc::clone(&shared);
let guard = clone.lock().unwrap();
```

### Common Mistakes and Fixes

| Mistake | Fix |
|---------|-----|
| Moving value, then using it | Use references & or clone |
| Multiple mutable references | Use RefCell with runtime checks |
| Returning reference to local | Return owned value instead |
| Forgetting lifetimes in struct | Add lifetime parameter <'a> |
| Using Rc across threads | Use Arc instead |
| Rc<RefCell<T>> without clone | Use Rc::clone(&rc) |

---

## ✅ PHASE 2 COMPLETE - CHECKLIST

You should now understand and be able to use:

- Ownership: Rules, move semantics, Copy vs Clone
- References: `&T` and `&mut T`, borrowing rules
- Lifetime annotations: `'a`, function signatures, structs
- Lifetime elision: When lifetimes can be omitted
- `'static` lifetime: String literals, thread spawning
- `Box<T>`: Heap allocation, recursive types, trait objects
- `Rc<T>`: Reference counting, shared ownership (single-threaded)
- `Arc<T>`: Atomic reference counting (multi-threaded)
- `RefCell<T>`: Interior mutability, runtime borrow checking
- `Mutex<T>`: Thread-safe interior mutability
- Smart pointer combinations: `Rc<RefCell<T>>`, `Arc<Mutex<T>>`
- Deref and Drop traits: How smart pointers work internally

---

## 🎉 Congratulations on Completing Phase 2! 🎉

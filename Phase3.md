# Phase 3: Collections, Strings & Error Handling 🚀

## Table of Contents
1. [Vectors (Vec<T>)](#1️⃣-vectors-vect)
2. [HashMaps](#2️⃣-hashmaps)
3. [Strings & &str](#3️⃣-strings-string-and-str)
4. [Error Handling (Result, Option, ?)](#4️⃣-error-handling-result-option-)
5. [Custom Data Structures](#5️⃣-custom-data-structures)
6. [Implementing Methods](#6️⃣-implementing-methods)

---

## 1️⃣ VECTORS (Vec<T>)

### 📖 What is it?

A **Vector** is a growable, dynamic array stored on the heap. It can grow and shrink at runtime.

- **Ownership**: Owns its data
- **Mutability**: Can be mutable or immutable
- **Performance**: O(1) access, amortized O(1) push/pop
- **Memory**: Contiguous heap allocation

### 🎯 When to Use

- When you need a collection of items
- When size is unknown at compile time
- When you need to add/remove items dynamically
- When you want indexable access

### 💡 Why Use Vectors

- **Flexibility**: Grows as needed
- **Safety**: Bounds checking on access
- **Efficiency**: Contiguous memory = cache friendly
- **Ergonomics**: Rich API with methods

### 📝 Complete Methods Reference

#### Creation Methods

| Method | Description | Example |
|--------|-------------|---------|
| `Vec::new()` | Empty vector | `let v: Vec<i32> = Vec::new();` |
| `vec![]` macro | Vector literal | `let v = vec![1, 2, 3];` |
| `Vec::with_capacity(n)` | Pre-allocate capacity | `let v: Vec<i32> = Vec::with_capacity(10);` |
| `vec![val; n]` | Repeat value n times | `vec![0; 5]` creates `[0, 0, 0, 0, 0]` |

```rust
fn vector_creation_examples() {
    // Empty vector - type must be specified or inferred
    let v1: Vec<i32> = Vec::new();
    
    // Using vec! macro (preferred)
    let v2 = vec![1, 2, 3];
    
    // With type annotation
    let v3: Vec<String> = Vec::new();
    
    // Pre-allocate capacity (more efficient for known size)
    let mut v4: Vec<i32> = Vec::with_capacity(100);
    
    // Repeat value n times
    let v5 = vec![0; 5]; // [0, 0, 0, 0, 0]
    
    // From iterator
    let v6: Vec<i32> = (1..=5).collect();
    
    // From slice
    let slice = &[1, 2, 3];
    let v7 = slice.to_vec();
    
    // From string chars
    let chars: Vec<char> = "hello".chars().collect();
    
    // From another vector
    let v8 = v2.clone();
}
```

#### Adding Elements

| Method | Description | Complexity |
|--------|-------------|-----------|
| `push(T)` | Add to end | O(1) amortized |
| `insert(pos, T)` | Insert at position | O(n) |
| `append(&mut other)` | Add all items from another vec | O(n) |
| `extend(iter)` | Add items from iterator | O(n) |

```rust
fn vector_add_examples() {
    let mut v = vec![1, 2, 3];
    
    // push - add to end (most common)
    v.push(4);
    println!("After push: {:?}", v); // [1, 2, 3, 4]
    
    // insert - add at position
    v.insert(0, 0);
    println!("After insert at 0: {:?}", v); // [0, 1, 2, 3, 4]
    
    v.insert(2, 1.5);
    // Can't mix types!
    
    // append - move all items from another vector
    let mut v1 = vec![1, 2];
    let mut v2 = vec![3, 4];
    v1.append(&mut v2);
    println!("After append: {:?}", v1); // [1, 2, 3, 4]
    println!("v2 is now empty: {:?}", v2); // []
    
    // extend - add from iterator
    v.extend(vec![5, 6]);
    v.extend([7, 8].iter());
    v.extend(9..=10);
    println!("After extends: {:?}", v);
}
```

#### Removing Elements

| Method | Description | Returns |
|--------|-------------|---------|
| `pop()` | Remove last element | `Option<T>` |
| `remove(pos)` | Remove at position | `T` (panics if invalid) |
| `clear()` | Remove all elements | `()` |
| `retain(pred)` | Keep only matching | `()` |

```rust
fn vector_remove_examples() {
    let mut v = vec![1, 2, 3, 4, 5];
    
    // pop - remove from end
    let last = v.pop();
    println!("Popped: {:?}", last); // Some(5)
    println!("After pop: {:?}", v); // [1, 2, 3, 4]
    
    let last = v.pop();
    println!("Popped: {:?}", last); // Some(4)
    
    // pop on empty vector
    let last = v.pop();
    println!("Popped from empty: {:?}", last); // None
    
    // remove - remove at specific index
    let mut v = vec!['a', 'b', 'c', 'd'];
    let removed = v.remove(1);
    println!("Removed: {}", removed); // 'b'
    println!("After remove(1): {:?}", v); // ['a', 'c', 'd']
    
    // remove causes shift, so use carefully
    let mut v = vec![1, 2, 3, 4, 5];
    v.remove(0);
    println!("After removing first: {:?}", v); // [2, 3, 4, 5]
    
    // clear - remove all
    v.clear();
    println!("After clear: {:?}", v); // []
    println!("Capacity preserved: {}", v.capacity()); // Still has capacity
    
    // retain - keep only matching elements
    let mut v = vec![1, 2, 3, 4, 5, 6];
    v.retain(|&x| x % 2 == 0);
    println!("After retain (evens): {:?}", v); // [2, 4, 6]
}
```

#### Access Methods

| Method | Returns | Panics? | Notes |
|--------|---------|---------|-------|
| `[index]` | `T` | Yes | Direct indexing |
| `get(index)` | `Option<&T>` | No | Safe access |
| `get_mut(index)` | `Option<&mut T>` | No | Mutable access |
| `first()` | `Option<&T>` | No | First element |
| `last()` | `Option<&T>` | No | Last element |

```rust
fn vector_access_examples() {
    let v = vec![10, 20, 30, 40, 50];
    
    // Direct indexing (panics on out of bounds)
    println!("v[0] = {}", v[0]); // 10
    println!("v[4] = {}", v[4]); // 50
    // println!("v[10] = {}", v[10]); // ❌ Panics!
    
    // Safe access with get() - returns Option
    match v.get(0) {
        Some(val) => println!("First: {}", val), // 10
        None => println!("Empty"),
    }
    
    match v.get(10) {
        Some(val) => println!("Index 10: {}", val),
        None => println!("Index 10 out of bounds"), // This prints
    }
    
    // first() / last()
    println!("First: {:?}", v.first()); // Some(&10)
    println!("Last: {:?}", v.last()); // Some(&50)
    
    let empty: Vec<i32> = Vec::new();
    println!("Empty first: {:?}", empty.first()); // None
    
    // Mutable access
    let mut v = vec![1, 2, 3];
    if let Some(first) = v.get_mut(0) {
        *first = 10;
    }
    println!("Modified: {:?}", v); // [10, 2, 3]
    
    // Slicing (immutable)
    let slice = &v[0..2];
    println!("Slice: {:?}", slice); // [10, 2]
    
    // Mutable slicing
    let mut_slice = &mut v[0..2];
    mut_slice[0] = 100;
    println!("After slice modify: {:?}", v); // [100, 2, 3]
}
```

#### Information Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `len()` | `usize` | Number of elements |
| `is_empty()` | `bool` | Check if empty |
| `capacity()` | `usize` | Allocated capacity |
| `contains(&T)` | `bool` | Check if contains |

```rust
fn vector_info_examples() {
    let mut v = vec![1, 2, 3];
    
    println!("Length: {}", v.len()); // 3
    println!("Is empty: {}", v.is_empty()); // false
    println!("Capacity: {}", v.capacity()); // >= 3
    
    // Capacity grows as vector grows
    let mut v: Vec<i32> = Vec::new();
    println!("Initial capacity: {}", v.capacity()); // 0
    
    for i in 1..=100 {
        v.push(i);
        if i == 1 || i == 2 || i == 4 || i == 8 || i == 16 || i == 32 || i == 64 {
            println!("After push {}: capacity = {}", i, v.capacity());
        }
    }
    
    // contains
    let v = vec![1, 2, 3, 4, 5];
    println!("Contains 3: {}", v.contains(&3)); // true
    println!("Contains 10: {}", v.contains(&10)); // false
}
```

#### Iteration Methods

| Method | Type | Notes |
|--------|------|-------|
| `iter()` | `Iter<T>` | Immutable references |
| `iter_mut()` | `IterMut<T>` | Mutable references |
| `into_iter()` | `IntoIter<T>` | Consumes vector |

```rust
fn vector_iteration_examples() {
    let v = vec![1, 2, 3, 4, 5];
    
    // Immutable iteration
    println!("Immutable iteration:");
    for item in &v {
        println!("  {}", item);
    }
    // v is still available here
    println!("v still available: {:?}", v);
    
    // Or using iter() explicitly
    for item in v.iter() {
        println!("  {}", item);
    }
    
    // Mutable iteration
    let mut v = vec![1, 2, 3];
    println!("\nMutable iteration:");
    for item in &mut v {
        *item *= 2;
    }
    println!("After doubling: {:?}", v); // [2, 4, 6]
    
    // Or using iter_mut() explicitly
    for item in v.iter_mut() {
        *item += 10;
    }
    println!("After adding 10: {:?}", v); // [12, 14, 16]
    
    // Consuming iteration (into_iter)
    let v = vec![1, 2, 3];
    println!("\nConsuming iteration:");
    for item in v {
        println!("  {}", item);
    }
    // println!("v: {:?}", v); // ❌ Error! v was consumed
    
    // With enumerate
    let v = vec!['a', 'b', 'c'];
    for (index, item) in v.iter().enumerate() {
        println!("  [{}] = {}", index, item);
    }
    
    // With enumerate on mutable
    let mut v = vec![1, 2, 3];
    for (i, item) in v.iter_mut().enumerate() {
        *item = i as i32;
    }
    println!("After enumerate: {:?}", v); // [0, 1, 2]
}
```

#### Iterator Adaptors

```rust
fn vector_iterator_adaptors_examples() {
    let v = vec![1, 2, 3, 4, 5];
    
    // map - transform each element
    let doubled: Vec<i32> = v.iter().map(|&x| x * 2).collect();
    println!("Doubled: {:?}", doubled); // [2, 4, 6, 8, 10]
    
    // filter - keep matching elements
    let evens: Vec<i32> = v.iter().filter(|&&x| x % 2 == 0).map(|&x| x).collect();
    println!("Evens: {:?}", evens); // [2, 4]
    
    // filter_map - filter and transform
    let results: Vec<i32> = v.iter()
        .filter_map(|&x| {
            if x > 2 { Some(x * 10) } else { None }
        })
        .collect();
    println!("Filter-map: {:?}", results); // [30, 40, 50]
    
    // fold/reduce - combine elements
    let sum: i32 = v.iter().fold(0, |acc, x| acc + x);
    println!("Sum: {}", sum); // 15
    
    let product: i32 = v.iter().fold(1, |acc, &x| acc * x);
    println!("Product: {}", product); // 120
    
    // any/all - predicates
    let has_even = v.iter().any(|&x| x % 2 == 0);
    println!("Has even: {}", has_even); // true
    
    let all_positive = v.iter().all(|&x| x > 0);
    println!("All positive: {}", all_positive); // true
    
    // find - first matching
    let first_even = v.iter().find(|&&x| x % 2 == 0);
    println!("First even: {:?}", first_even); // Some(2)
    
    // take/skip
    let first_three: Vec<i32> = v.iter().take(3).map(|&x| x).collect();
    println!("First 3: {:?}", first_three); // [1, 2, 3]
    
    let skip_two: Vec<i32> = v.iter().skip(2).map(|&x| x).collect();
    println!("Skip 2: {:?}", skip_two); // [3, 4, 5]
    
    // zip
    let v1 = vec![1, 2, 3];
    let v2 = vec!['a', 'b', 'c'];
    let zipped: Vec<(i32, char)> = v1.iter().zip(v2.iter()).map(|(&a, &b)| (a, b)).collect();
    println!("Zipped: {:?}", zipped); // [(1, 'a'), (2, 'b'), (3, 'c')]
    
    // rev - reverse
    let reversed: Vec<i32> = v.iter().rev().map(|&x| x).collect();
    println!("Reversed: {:?}", reversed); // [5, 4, 3, 2, 1]
}
```

#### Sorting

```rust
fn vector_sorting_examples() {
    // sort() - in place, requires Ord trait
    let mut v = vec![3, 1, 4, 1, 5, 9, 2, 6];
    v.sort();
    println!("Sorted: {:?}", v); // [1, 1, 2, 3, 4, 5, 6, 9]
    
    // sort_unstable() - faster, doesn't preserve order of equal elements
    let mut v = vec![3, 1, 4, 1, 5, 9, 2, 6];
    v.sort_unstable();
    println!("Sort unstable: {:?}", v); // [1, 1, 2, 3, 4, 5, 6, 9]
    
    // sort_by() - custom comparator
    let mut v = vec![3, 1, 4, 1, 5];
    v.sort_by(|a, b| b.cmp(a)); // Reverse order
    println!("Reverse: {:?}", v); // [5, 4, 3, 1, 1]
    
    // sort_by_key() - sort by property
    #[derive(Debug)]
    struct Person {
        name: String,
        age: u32,
    }
    
    let mut people = vec![
        Person { name: "Alice".to_string(), age: 30 },
        Person { name: "Bob".to_string(), age: 25 },
        Person { name: "Carol".to_string(), age: 35 },
    ];
    
    people.sort_by_key(|p| p.age);
    println!("Sorted by age: {:?}", people);
    
    // Reverse sort by key
    people.sort_by_key(|p| std::cmp::Reverse(p.age));
    println!("Reverse by age: {:?}", people);
    
    // String sorting
    let mut words = vec!["zebra", "apple", "mango", "banana"];
    words.sort();
    println!("Sorted words: {:?}", words); // ["apple", "banana", "mango", "zebra"]
    
    // Case-insensitive sort
    let mut words = vec!["Zebra", "apple", "Mango", "BANANA"];
    words.sort_by(|a, b| a.to_lowercase().cmp(&b.to_lowercase()));
    println!("Case-insensitive: {:?}", words);
}
```

#### Searching (Binary Search)

```rust
fn vector_search_examples() {
    let v = vec![1, 3, 5, 7, 9, 11];
    
    // binary_search() - requires sorted vector
    match v.binary_search(&5) {
        Ok(index) => println!("Found 5 at index {}", index), // Found 5 at index 2
        Err(index) => println!("5 not found, would be inserted at {}", index),
    }
    
    match v.binary_search(&4) {
        Ok(index) => println!("Found 4 at index {}", index),
        Err(index) => println!("4 not found, would be inserted at {}", index), // at 2
    }
    
    // binary_search_by() - custom comparator
    #[derive(Debug)]
    struct Point { x: i32, y: i32 }
    
    let points = vec![
        Point { x: 1, y: 1 },
        Point { x: 2, y: 2 },
        Point { x: 3, y: 3 },
    ];
    
    match points.binary_search_by(|p| p.x.cmp(&2)) {
        Ok(i) => println!("Found point at {}: {:?}", i, points[i]),
        Err(i) => println!("Not found"),
    }
}
```

#### Transformation Methods

```rust
fn vector_transformation_examples() {
    // reverse
    let mut v = vec![1, 2, 3, 4, 5];
    v.reverse();
    println!("Reversed: {:?}", v); // [5, 4, 3, 2, 1]
    
    // dedup - remove consecutive duplicates
    let mut v = vec![1, 1, 2, 2, 2, 3, 1, 1, 4];
    v.dedup();
    println!("After dedup: {:?}", v); // [1, 2, 3, 1, 4]
    
    // dedup_by - custom comparison
    let mut v = vec!["a", "A", "b", "B"];
    v.dedup_by(|a, b| a.eq_ignore_ascii_case(b));
    println!("Case-insensitive dedup: {:?}", v); // ["a", "b"]
    
    // split_off
    let mut v1 = vec![1, 2, 3, 4, 5];
    let v2 = v1.split_off(3);
    println!("First: {:?}", v1); // [1, 2, 3]
    println!("Second: {:?}", v2); // [4, 5]
}
```

#### Capacity Management

```rust
fn vector_capacity_examples() {
    let mut v: Vec<i32> = Vec::with_capacity(10);
    println!("Initial capacity: {}", v.capacity()); // 10
    
    for i in 1..=10 {
        v.push(i);
    }
    println!("After 10 pushes - len: {}, capacity: {}", v.len(), v.capacity()); // 10, 10
    
    v.push(11); // Need to grow
    println!("After 11th push - len: {}, capacity: {}", v.len(), v.capacity()); // 11, 20 (doubled)
    
    // shrink_to_fit - reduces capacity to length
    v.shrink_to_fit();
    println!("After shrink - len: {}, capacity: {}", v.len(), v.capacity()); // 11, 11
    
    // shrink_to - shrink to at least this capacity
    let mut v = Vec::with_capacity(100);
    v.extend_from_slice(&[1, 2, 3]);
    println!("Before shrink - capacity: {}", v.capacity()); // 100
    v.shrink_to(50);
    println!("After shrink_to(50) - capacity: {}", v.capacity()); // >= 50
}
```

### ⚡ Performance Recommendations

```rust
// ✅ GOOD: Pre-allocate capacity when size is known
let mut vec = Vec::with_capacity(1000);
for i in 0..1000 {
    vec.push(i);
}

// ❌ BAD: Repeated reallocation
let mut vec = Vec::new();
for i in 0..1000 {
    vec.push(i); // Reallocates multiple times
}

// ✅ GOOD: Use iterators for transformations
let doubled: Vec<i32> = original.iter().map(|&x| x * 2).collect();

// ❌ BAD: Manual loop
let mut doubled = Vec::new();
for &x in &original {
    doubled.push(x * 2);
}

// ✅ GOOD: Use references in loops when you don't need ownership
for item in &vec {
    println!("{}", item);
}

// ✅ GOOD: Use into_iter when you want to consume
for item in vec {
    println!("{}", item);
}

// ❌ BAD: Cloning for loops
for item in vec.clone().iter() {
    println!("{}", item);
}

// ✅ GOOD: Use iterators for chaining
let result: Vec<i32> = data.iter()
    .filter(|&x| x > 0)
    .map(|&x| x * 2)
    .collect();

// ✅ GOOD: Use binary_search on sorted vectors
let mut vec = vec![1, 3, 5, 7, 9];
vec.binary_search(&5); // O(log n)

// ❌ BAD: Linear search on sorted vectors
let pos = vec.iter().position(|&x| x == 5); // O(n)
```

### 🏆 Best Practices

```rust
// 1. Prefer vec! macro for literals
let v = vec![1, 2, 3]; // Good
let v: Vec<i32> = Vec::new(); // Less common

// 2. Use &str for function parameters, not Vec<String>
fn process_items(items: &[String]) { // Takes slice
    for item in items {
        println!("{}", item);
    }
}

// 3. Use iterators over indexing
for item in &vec { // Good
    println!("{}", item);
}

for i in 0..vec.len() { // Avoid unless you need index
    println!("{}", vec[i]);
}

// 4. Use get() for safe access
if let Some(item) = vec.get(5) { // Safe
    println!("{}", item);
}

// 5. Reserve capacity for known sizes
let mut vec = Vec::with_capacity(1000);
for item in items {
    vec.push(item);
}

// 6. Chain operations with iterators
let result: i32 = vec.iter()
    .filter(|&&x| x > 0)
    .map(|&x| x * 2)
    .sum();

// 7. Use extend_from_slice for known slices
vec.extend_from_slice(&[1, 2, 3]);

// 8. Avoid repeated clones
let vec = vec![1, 2, 3];
let copy = &vec; // Borrow instead of clone
```

---

## 2️⃣ HASHMAPS

### 📖 What is it?

A **HashMap** is an unordered collection of key-value pairs. It uses hashing to achieve O(1) average lookup time.

- **Ownership**: Owns both keys and values
- **Keys**: Must implement `Hash` and `Eq` traits
- **Performance**: O(1) average access, O(n) worst case
- **Order**: Unordered (order of iteration not guaranteed)

### 🎯 When to Use

- Looking up values by unique keys
- Counting occurrences of items
- Caching/memoization
- Building dictionaries or maps

### 💡 Why Use HashMaps

- **Speed**: O(1) lookup instead of O(n)
- **Flexibility**: Any `Hash + Eq` type as key
- **Expressiveness**: Represents real-world mappings
- **Rich API**: Entry API for efficient updates

### 📝 Complete Methods Reference

#### Creation Methods

| Method | Description | Example |
|--------|-------------|---------|
| `HashMap::new()` | Create empty map | `let map = HashMap::new();` |
| `HashMap::with_capacity(n)` | Pre-allocate for n entries | `HashMap::with_capacity(100)` |
| `collect()` | From iterator of pairs | `vec![(k,v)].into_iter().collect()` |

```rust
use std::collections::HashMap;

fn hashmap_creation_examples() {
    // Empty HashMap
    let map: HashMap<String, i32> = HashMap::new();
    
    // Using from
    let mut map = HashMap::new();
    map.insert("a".to_string(), 1);
    
    // With capacity (optimizes initial allocation)
    let map: HashMap<String, i32> = HashMap::with_capacity(100);
    
    // From vector of tuples
    let pairs = vec![
        ("name".to_string(), "Alice".to_string()),
        ("city".to_string(), "NYC".to_string()),
    ];
    let map: HashMap<String, String> = pairs.into_iter().collect();
    
    // From arrays with into_iter
    let pairs = [("a", 1), ("b", 2), ("c", 3)];
    let map: HashMap<_, _> = pairs.into_iter().collect();
}
```

#### Insertion Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `insert(k, v)` | Insert or update | `Option<V>` (old value if existed) |
| `entry(k)` | Entry API for complex operations | `Entry<K, V>` |

```rust
fn hashmap_insert_examples() {
    let mut map = HashMap::new();
    
    // insert - returns None if new, Some(old_value) if existed
    let old = map.insert("apple".to_string(), 1);
    println!("Inserted 'apple': {:?}", old); // None
    
    let old = map.insert("apple".to_string(), 2);
    println!("Updated 'apple': {:?}", old); // Some(1)
    
    println!("Map: {:?}", map);
}
```

#### Access Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `get(&key)` | `Option<&V>` | Get reference to value |
| `get_mut(&key)` | `Option<&mut V>` | Get mutable reference |
| `contains_key(&key)` | `bool` | Check if key exists |

```rust
fn hashmap_access_examples() {
    let mut scores = HashMap::new();
    scores.insert("Alice".to_string(), 100);
    scores.insert("Bob".to_string(), 85);
    
    // get - safe access
    match scores.get("Alice") {
        Some(&score) => println!("Alice's score: {}", score),
        None => println!("Not found"),
    }
    
    // get with if-let
    if let Some(score) = scores.get("Alice") {
        println!("Alice: {}", score);
    }
    
    // contains_key
    if scores.contains_key("Bob") {
        println!("Bob's score: {}", scores["Bob"]);
    }
    
    // Direct indexing (panics if not found)
    println!("Alice: {}", scores["Alice"]); // 100
    // println!("Carol: {}", scores["Carol"]); // ❌ Panics!
    
    // Mutable access
    if let Some(score) = scores.get_mut("Bob") {
        *score += 5;
    }
    println!("Updated scores: {:?}", scores);
}
```

#### Removal Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `remove(&key)` | `Option<V>` | Remove and get value |
| `clear()` | `()` | Remove all entries |

```rust
fn hashmap_removal_examples() {
    let mut map = HashMap::new();
    map.insert("a".to_string(), 1);
    map.insert("b".to_string(), 2);
    map.insert("c".to_string(), 3);
    
    // remove - returns value if existed
    let removed = map.remove("a");
    println!("Removed: {:?}", removed); // Some(1)
    println!("After removal: {:?}", map); // {"b": 2, "c": 3}
    
    let removed = map.remove("x");
    println!("Not found: {:?}", removed); // None
    
    // clear - remove all
    map.clear();
    println!("After clear: {:?}", map); // {}
    println!("Length: {}", map.len()); // 0
}
```

#### Iteration Methods

| Method | Type | Ownership |
|--------|------|-----------|
| `iter()` | `Iter<K, V>` | Immutable references |
| `iter_mut()` | `IterMut<K, V>` | Mutable values only |
| `into_iter()` | `IntoIter<K, V>` | Consumes map |

```rust
fn hashmap_iteration_examples() {
    let mut scores = HashMap::new();
    scores.insert("Alice".to_string(), 100);
    scores.insert("Bob".to_string(), 85);
    scores.insert("Carol".to_string(), 92);
    
    // Iterate over entries
    println!("All scores:");
    for (name, score) in &scores {
        println!("  {}: {}", name, score);
    }
    // scores still available
    
    // Iterate over keys only
    println!("Names:");
    for name in scores.keys() {
        println!("  {}", name);
    }
    
    // Iterate over values only
    println!("Scores:");
    for score in scores.values() {
        println!("  {}", score);
    }
    
    // Mutable iteration over values
    let mut mutable_scores = scores.clone();
    for score in mutable_scores.values_mut() {
        *score += 5;
    }
    println!("After bonus: {:?}", mutable_scores);
    
    // Collect keys into vector
    let keys: Vec<&str> = scores.keys().cloned().collect();
    println!("Keys vector: {:?}", keys);
    
    // Collect values into vector
    let values: Vec<i32> = scores.values().cloned().collect();
    println!("Values vector: {:?}", values);
}
```

#### Entry API (Advanced Patterns)

```rust
fn hashmap_entry_api_patterns() {
    let mut map: HashMap<String, Vec<i32>> = HashMap::new();
    
    // Pattern 1: Add value to list
    map.entry("numbers".to_string())
        .or_insert(Vec::new())
        .push(42);
    
    // Pattern 2: Update or insert with complex logic
    let mut counters = HashMap::new();
    for i in 0..10 {
        counters.entry(i % 3)
            .and_modify(|count| *count += 1)
            .or_insert(1);
    }
    println!("Counters: {:?}", counters); // {0: 4, 1: 3, 2: 3}
    
    // Pattern 3: Default with computation
    let mut cache: HashMap<String, String> = HashMap::new();
    let key = "expensive".to_string();
    
    let value = cache.entry(key.clone())
        .or_insert_with(|| {
            println!("Computing expensive value...");
            format!("Result for {}", key)
        });
    
    // Pattern 4: Chaining operations
    let mut scores = HashMap::new();
    scores.insert("Alice".to_string(), 100);
    
    scores.entry("Alice".to_string())
        .and_modify(|s| *s += 10)
        .or_insert(0);
    
    scores.entry("Bob".to_string())
        .and_modify(|s| *s += 10)
        .or_insert(50);
    
    println!("{:?}", scores); // {"Alice": 110, "Bob": 50}
}
```

### ⚡ Performance Recommendations

```rust
// ✅ GOOD: Pre-allocate capacity when size is known
let mut map = HashMap::with_capacity(1000);
for i in 0..1000 {
    map.insert(i, i * 2);
}

// ✅ GOOD: Use entry API for single lookup
*map.entry(key).or_insert(0) += 1; // Single hash computation

// ❌ BAD: Multiple lookups
if map.contains_key(key) {
    *map.get_mut(key).unwrap() += 1;
} else {
    map.insert(key.clone(), 1);
}

// ✅ GOOD: Use iterators for transformation
let new_map: HashMap<_, _> = old_map.iter()
    .map(|(k, v)| (k.clone(), v * 2))
    .collect();

// ✅ GOOD: Reserve capacity when building
fn build_map(items: &[(String, i32)]) -> HashMap<String, i32> {
    let mut map = HashMap::with_capacity(items.len());
    for (k, v) in items {
        map.insert(k.clone(), *v);
    }
    map
}
```

### 🏆 Best Practices

```rust
// 1. Always use entry API for conditional insertion
fn increment_counter(map: &mut HashMap<String, i32>, key: &str) {
    *map.entry(key.to_string()).or_insert(0) += 1;
}

// 2. Use or_insert_with for expensive defaults
fn get_cached_value(map: &mut HashMap<String, String>, key: &str) -> &String {
    map.entry(key.to_string())
        .or_insert_with(|| expensive_computation(key))
}

// 3. Use iterators for bulk operations
fn transform_values(map: &HashMap<String, i32>, factor: i32) -> HashMap<String, i32> {
    map.iter()
        .map(|(k, v)| (k.clone(), v * factor))
        .collect()
}

// 4. Use get() for safe access, not indexing
if let Some(value) = map.get(key) {
    process(value);
}

// 5. Clear and reuse to avoid reallocation
let mut map = HashMap::with_capacity(1000);
// ... use map ...
map.clear(); // Keeps capacity

// 6. Use appropriate key types
// Prefer &str over String for lookups when possible
let scores: HashMap<String, i32> = HashMap::new();
let lookup = "Alice";
if let Some(score) = scores.get(lookup) { // Works with &str
    println!("{}", score);
}
```

---

## 3️⃣ STRINGS (String and &str)

### 📖 What is it?

Rust has two main string types:

- **String**: Owned, heap-allocated, mutable, growable UTF-8 string
- **&str**: Borrowed, immutable view into a string (string slice)

### 🎯 When to Use

- **String**: When you own the data, need to modify it, or pass it around
- **&str**: For function parameters, string literals, and read-only access
- **&str for reading**: Always prefer &str for function parameters

### 💡 Why Use Rust Strings

- **Safety**: UTF-8 guaranteed, no null terminators
- **Memory safe**: Ownership prevents use-after-free
- **Efficient**: No copying with slices

### 📝 Complete Methods Reference

#### Creation Methods

```rust
fn string_creation_examples() {
    // String (owned, mutable)
    let s1 = String::new();
    let s2 = String::from("hello");
    let s3 = "hello".to_string();
    let s4 = "hello".to_owned();
    let s5 = format!("{} {}", "hello", "world");
    
    // &str (borrowed, immutable)
    let literal: &str = "hello";
    let slice: &str = &s2;
    let substring: &str = &s2[0..3];
    
    // From characters
    let chars = vec!['h', 'e', 'l', 'l', 'o'];
    let s: String = chars.into_iter().collect();
    
    // From bytes (fallible)
    let bytes = vec![104, 101, 108, 108, 111];
    let s = String::from_utf8(bytes).unwrap();
}
```

#### Manipulation Methods

| Method | Description | Example |
|--------|-------------|---------|
| `push(c)` | Add character | `s.push('!');` |
| `push_str(str)` | Add string slice | `s.push_str(" world");` |
| `insert(pos, c)` | Insert character | `s.insert(5, ' ');` |
| `insert_str(pos, str)` | Insert string | `s.insert_str(0, "Hello ");` |
| `pop()` | Remove last char | `let c = s.pop();` |
| `remove(pos)` | Remove char at pos | `let c = s.remove(5);` |
| `truncate(len)` | Truncate to len | `s.truncate(5);` |
| `clear()` | Empty string | `s.clear();` |

```rust
fn string_manipulation_examples() {
    let mut s = String::from("Hello");
    
    // push - add character
    s.push('!');
    println!("After push: {}", s); // "Hello!"
    
    // push_str - add string slice
    s.push_str(" How are you?");
    println!("After push_str: {}", s); // "Hello! How are you?"
    
    // insert - add character at position
    s.insert(5, ' ');
    println!("After insert: {}", s); // "Hello ! How are you?"
    
    // insert_str - add string at position
    s.insert_str(6, "beautiful ");
    println!("After insert_str: {}", s); // "Hello beautiful ! How are you?"
    
    // pop - remove last character
    let last = s.pop();
    println!("Popped: {:?}, String: {}", last, s); // Some('?'), "Hello beautiful ! How are you"
    
    // remove - remove character at index
    let removed = s.remove(6); // removes space
    println!("Removed: '{}', String: {}", removed, s); // ' ', "Hello beautiful! How are you"
    
    // truncate - shorten string
    s.truncate(5);
    println!("Truncated: {}", s); // "Hello"
    
    // clear - empty string
    s.clear();
    println!("Cleared: '{}'", s); // ""
}
```

#### Access and Info Methods

| Method | Return | Description |
|--------|--------|-------------|
| `len()` | `usize` | Length in bytes |
| `is_empty()` | `bool` | Check if empty |
| `capacity()` | `usize` | Allocated capacity |
| `chars()` | `Chars` | Iterator over Unicode chars |
| `bytes()` | `Bytes` | Iterator over raw bytes |
| `as_bytes()` | `&[u8]` | As byte slice |
| `as_str()` | `&str` | As string slice |
| `get(i..j)` | `Option<&str>` | Safe slicing |

```rust
fn string_access_examples() {
    let s = String::from("Hello 世界");
    
    // Length in bytes (not characters!)
    println!("Bytes length: {}", s.len()); // 11 (5 + 1 + 6)
    
    // Character count
    println!("Character count: {}", s.chars().count()); // 7
    
    // Check if empty
    println!("Is empty: {}", s.is_empty()); // false
    
    // Capacity
    println!("Capacity: {}", s.capacity());
    
    // As bytes
    let bytes = s.as_bytes();
    println!("First byte: {}", bytes[0]); // 72 (ASCII 'H')
    
    // Iterate over characters
    println!("Characters:");
    for c in s.chars() {
        println!("  {}", c);
    }
    
    // Iterate over bytes
    println!("Bytes:");
    for b in s.bytes() {
        print!("{} ", b);
    }
    println!();
    
    // Safe slicing (returns Option)
    if let Some(slice) = s.get(0..5) {
        println!("First 5 bytes: {}", slice); // "Hello"
    }
}
```

#### Searching Methods

| Method | Return | Description |
|--------|--------|-------------|
| `contains(pattern)` | `bool` | Check if contains |
| `starts_with(pattern)` | `bool` | Check prefix |
| `ends_with(pattern)` | `bool` | Check suffix |
| `find(pattern)` | `Option<usize>` | First match index |
| `rfind(pattern)` | `Option<usize>` | Last match index |
| `match_indices(pattern)` | `Iterator` | Matches with indices |

```rust
fn string_search_examples() {
    let s = "Hello world, welcome to Rust programming";
    
    // contains
    if s.contains("world") {
        println!("Contains 'world'");
    }
    
    // starts_with / ends_with
    println!("Starts with 'Hello': {}", s.starts_with("Hello")); // true
    println!("Ends with 'Rust': {}", s.ends_with("Rust")); // false
    println!("Ends with 'programming': {}", s.ends_with("programming")); // true
    
    // find - first occurrence
    if let Some(pos) = s.find("world") {
        println!("'world' found at position: {}", pos); // 6
    }
    
    if let Some(pos) = s.find("o") {
        println!("First 'o' at: {}", pos); // 4
    }
    
    // rfind - last occurrence
    if let Some(pos) = s.rfind("o") {
        println!("Last 'o' at: {}", pos); // 26
    }
    
    // match_indices - all matches with positions
    println!("All 'o' positions:");
    for (pos, match_str) in s.match_indices("o") {
        println!("  {} at {}", match_str, pos);
    }
}
```

#### Splitting Methods

| Method | Description | Example |
|--------|-------------|---------|
| `split(delimiter)` | Split by delimiter | `s.split(",")` |
| `split_whitespace()` | Split by whitespace | `s.split_whitespace()` |
| `splitn(n, delimiter)` | Split n times | `s.splitn(2, ",")` |
| `lines()` | Split by line breaks | `s.lines()` |

```rust
fn string_split_examples() {
    let s = "apple,banana,orange,grape";
    
    // split by delimiter
    println!("Split by comma:");
    for fruit in s.split(',') {
        println!("  {}", fruit);
    }
    
    // collect into vector
    let fruits: Vec<&str> = s.split(',').collect();
    println!("Fruits: {:?}", fruits);
    
    // splitn - limit number of splits
    let parts: Vec<&str> = s.splitn(3, ',').collect();
    println!("Split into 3: {:?}", parts); // ["apple", "banana", "orange,grape"]
    
    // split_whitespace
    let text = "Hello   world\tfrom\nRust";
    let words: Vec<&str> = text.split_whitespace().collect();
    println!("Words: {:?}", words); // ["Hello", "world", "from", "Rust"]
    
    // lines
    let multiline = "Line 1\nLine 2\nLine 3";
    for line in multiline.lines() {
        println!("Line: {}", line);
    }
}
```

#### Replacement and Transformation

| Method | Description | Example |
|--------|-------------|---------|
| `replace(from, to)` | Replace all | `s.replace("old", "new")` |
| `replacen(from, to, n)` | Replace n times | `s.replacen("a", "b", 2)` |
| `to_lowercase()` | Convert to lowercase | `s.to_lowercase()` |
| `to_uppercase()` | Convert to uppercase | `s.to_uppercase()` |
| `trim()` | Trim whitespace both ends | `s.trim()` |

```rust
fn string_transform_examples() {
    let s = "  Hello World, Hello Rust  ";
    
    // trim whitespace
    println!("Trimmed: '{}'", s.trim());
    println!("Trim start: '{}'", s.trim_start());
    println!("Trim end: '{}'", s.trim_end());
    
    // case conversion
    println!("Lowercase: {}", s.to_lowercase());
    println!("Uppercase: {}", s.to_uppercase());
    
    // replace all
    let replaced = s.replace("Hello", "Hi");
    println!("Replace all: {}", replaced);
    
    // replace limited
    let replaced = s.replacen("Hello", "Hi", 1);
    println!("Replace first: {}", replaced);
    
    // chaining transformations
    let result = s.trim()
        .to_lowercase()
        .replace("hello", "hi");
    println!("Chained: {}", result);
}
```

#### Concatenation Methods

```rust
fn string_concatenation_examples() {
    let s1 = String::from("Hello");
    let s2 = String::from("world");
    let s3 = "!";
    
    // + operator (moves s1)
    let result = s1 + " " + &s2 + s3;
    println!("With +: {}", result);
    
    // format! macro (doesn't move)
    let s1 = String::from("Hello");
    let s2 = String::from("world");
    let result = format!("{} {}!", s1, s2);
    println!("With format!: {}", result);
    println!("s1 and s2 still available: {} {}", s1, s2);
    
    // join for vectors
    let words = vec!["Hello", "world", "from", "Rust"];
    let sentence = words.join(" ");
    println!("Join: {}", sentence);
    
    // push_str for building (most efficient)
    let mut builder = String::with_capacity(50);
    builder.push_str("Hello");
    builder.push_str(" ");
    builder.push_str("world");
    builder.push_str("!");
    println!("Builder: {}", builder);
}
```

### ⚡ Performance Recommendations

```rust
// ✅ GOOD: Pre-allocate capacity when size is known
let mut s = String::with_capacity(1000);
for i in 0..1000 {
    s.push_str(&i.to_string());
}

// ❌ BAD: Multiple reallocations
let mut s = String::new();
for i in 0..1000 {
    s.push_str(&i.to_string()); // May reallocate many times
}

// ✅ GOOD: Use push_str for building
let mut s = String::with_capacity(50);
s.push_str("Hello");
s.push_str(" ");
s.push_str("world");

// ✅ GOOD: Use format! for complex formatting
let s = format!("{}: {} ({})", name, value, status);

// ✅ GOOD: Use &str for function parameters
fn process(text: &str) { // Accepts both String and &str
    println!("{}", text);
}
```

### 🏆 Best Practices

```rust
// 1. Use &str for function parameters (more flexible)
fn print_greeting(name: &str) {
    println!("Hello, {}!", name);
}

// Accepts both String and &str
let name = String::from("Alice");
print_greeting(&name);
print_greeting("Bob");

// 2. Use push_str for building strings efficiently
fn build_message(parts: &[&str]) -> String {
    let mut result = String::with_capacity(100);
    for part in parts {
        if !result.is_empty() {
            result.push(' ');
        }
        result.push_str(part);
    }
    result
}

// 3. Use format! for complex formatting
fn format_user(name: &str, age: u32, city: &str) -> String {
    format!("Name: {}, Age: {}, City: {}", name, age, city)
}

// 4. Use collect for joining iterators
let words = vec!["hello", "world", "rust"];
let sentence: String = words.iter().map(|&w| w.to_uppercase()).collect();

// 5. Prefer to_string() over String::from() for conciseness
let s = "hello".to_string(); // Idiomatic
```

---

## 4️⃣ ERROR HANDLING (Result, Option, ?)

### 📖 What is it?

Rust uses enums for error handling instead of exceptions:

- **Result<T, E>**: Represents success (Ok(T)) or failure (Err(E))
- **Option<T>**: Represents presence (Some(T)) or absence (None)
- **? operator**: Propagates errors up the call stack

### 🎯 When to Use

- **Result**: For operations that can fail (file I/O, parsing, network)
- **Option**: For values that might be absent (collection lookups)
- **? operator**: For propagating errors to caller

### 💡 Why Use Rust Error Handling

- **Explicit**: Errors are part of function signatures
- **Safe**: Must handle or propagate errors
- **Composable**: Rich combinator methods

### 📝 Complete Methods Reference

#### Result Methods

```rust
fn result_methods_examples() {
    let ok: Result<i32, &str> = Ok(42);
    let err: Result<i32, &str> = Err("error occurred");
    
    // Check status
    println!("is_ok: {}", ok.is_ok()); // true
    println!("is_err: {}", ok.is_err()); // false
    
    // Get value with defaults
    println!("unwrap_or: {}", err.unwrap_or(0)); // 0
    println!("unwrap_or_else: {}", err.unwrap_or_else(|e| e.len() as i32)); // 14
    
    // Transform
    let doubled = ok.map(|x| x * 2);
    println!("map: {:?}", doubled); // Ok(84)
    
    let with_err = err.map(|x| x * 2);
    println!("map on err: {:?}", with_err); // Err("error occurred")
    
    // Transform error
    let mapped_err = err.map_err(|e| format!("New: {}", e));
    println!("map_err: {:?}", mapped_err);
    
    // Chain operations
    let chained = ok.and_then(|x| if x > 0 { Ok(x * 2) } else { Err("negative") });
    println!("and_then: {:?}", chained); // Ok(84)
    
    // Convert to Option
    let as_option = ok.ok();
    println!("ok(): {:?}", as_option); // Some(42)
    
    // Match pattern (most common)
    match ok {
        Ok(value) => println!("Success: {}", value),
        Err(e) => println!("Error: {}", e),
    }
}
```

#### Option Methods

```rust
fn option_methods_examples() {
    let some: Option<i32> = Some(42);
    let none: Option<i32> = None;
    
    // Check status
    println!("is_some: {}", some.is_some()); // true
    println!("is_none: {}", some.is_none()); // false
    
    // Get value with defaults
    println!("unwrap_or: {}", none.unwrap_or(0)); // 0
    println!("unwrap_or_else: {}", none.unwrap_or_else(|| 42)); // 42
    
    // Transform
    let doubled = some.map(|x| x * 2);
    println!("map: {:?}", doubled); // Some(84)
    
    // Filter
    let filtered = some.filter(|&x| x > 50);
    println!("filter: {:?}", filtered); // None
    
    let filtered = some.filter(|&x| x > 30);
    println!("filter: {:?}", filtered); // Some(42)
    
    // Chain operations
    let chained = some.and_then(|x| if x > 0 { Some(x * 2) } else { None });
    println!("and_then: {:?}", chained); // Some(84)
    
    // Convert to Result
    let as_result = some.ok_or("no value");
    println!("ok_or: {:?}", as_result); // Ok(42)
    
    // Match pattern (most common)
    match some {
        Some(value) => println!("Found: {}", value),
        None => println!("Not found"),
    }
    
    // if let for single case
    if let Some(value) = some {
        println!("Value exists: {}", value);
    }
}
```

#### The ? Operator

```rust
use std::fs::File;
use std::io::{self, Read};

// Without ? (verbose)
fn read_file_verbose() -> Result<String, io::Error> {
    let file_result = File::open("hello.txt");
    
    let mut file = match file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };
    
    let mut contents = String::new();
    match file.read_to_string(&mut contents) {
        Ok(_) => Ok(contents),
        Err(e) => Err(e),
    }
}

// With ? (concise)
fn read_file_concise() -> Result<String, io::Error> {
    let mut file = File::open("hello.txt")?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}

// Even more concise
fn read_file_simplest() -> Result<String, io::Error> {
    let mut contents = String::new();
    File::open("hello.txt")?.read_to_string(&mut contents)?;
    Ok(contents)
}

// Using fs::read_to_string (most concise)
fn read_file_best() -> Result<String, io::Error> {
    std::fs::read_to_string("hello.txt")
}

// ? with Option
fn first_char(s: &str) -> Option<char> {
    s.chars().next()? // Returns None if no chars
}
```

#### Custom Error Types

```rust
use std::fmt;

// Custom error enum
#[derive(Debug)]
enum ValidationError {
    TooShort,
    TooLong,
    InvalidCharacter(char),
    Empty,
}

impl fmt::Display for ValidationError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            ValidationError::TooShort => write!(f, "Input is too short (min 3 chars)"),
            ValidationError::TooLong => write!(f, "Input is too long (max 20 chars)"),
            ValidationError::InvalidCharacter(c) => write!(f, "Invalid character: '{}'", c),
            ValidationError::Empty => write!(f, "Input cannot be empty"),
        }
    }
}

impl std::error::Error for ValidationError {}

// Usage
fn validate_username(name: &str) -> Result<(), ValidationError> {
    if name.is_empty() {
        return Err(ValidationError::Empty);
    }
    if name.len() < 3 {
        return Err(ValidationError::TooShort);
    }
    if name.len() > 20 {
        return Err(ValidationError::TooLong);
    }
    for c in name.chars() {
        if !c.is_alphanumeric() {
            return Err(ValidationError::InvalidCharacter(c));
        }
    }
    Ok(())
}
```

### ⚡ Performance Recommendations

```rust
// ✅ GOOD: Use ? operator for propagation
fn process() -> Result<i32, Error> {
    let data = read_file()?;
    let parsed = parse_data(&data)?;
    Ok(processed)
}

// ✅ GOOD: Use unwrap only when error can't happen
#[cfg(test)]
mod tests {
    #[test]
    fn test_parse() {
        let value = "42".parse::<i32>().unwrap(); // Test-only
        assert_eq!(value, 42);
    }
}

// ✅ GOOD: Use expect with context
let file = File::open("config.txt")
    .expect("Failed to open config.txt");

// ✅ GOOD: Use Box<dyn Error> for simple applications
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let data = std::fs::read_to_string("file.txt")?;
    let num: i32 = data.trim().parse()?;
    println!("{}", num);
    Ok(())
}
```

### 🏆 Best Practices

```rust
// 1. Always handle errors, don't panic
fn safe_divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

// 2. Use custom error types for libraries
#[derive(Debug)]
pub enum MyError {
    Io(std::io::Error),
    Parse(std::num::ParseIntError),
    Custom(String),
}

// 3. Use context with errors
fn read_number() -> Result<i32, std::io::Error> {
    let content = std::fs::read_to_string("number.txt")?;
    content.trim().parse()
        .map_err(|e| std::io::Error::new(std::io::ErrorKind::InvalidData, e))
}

// 4. Use Option for optional values, Result for errors
fn find_user(id: u32) -> Option<User> { ... } // Not an error if not found
fn load_user(id: u32) -> Result<User, Error> { ... } // Error if can't load

// 5. Document error cases
/// Parses a configuration string.
///
/// # Errors
///
/// Returns `ParseError::Empty` if the string is empty.
/// Returns `ParseError::InvalidFormat` if the format is invalid.
fn parse_config(s: &str) -> Result<Config, ParseError> { ... }
```

---

## 5️⃣ CUSTOM DATA STRUCTURES

### 📖 What is it?

Building your own data structures using Rust's type system, ownership rules, and generics.

### 🎯 When to Use

- When standard collections don't meet your needs
- For learning how data structures work
- When you need specialized behavior or performance
- For teaching or educational purposes

### 💡 Why Build Custom Data Structures

- **Control**: Full control over memory layout and behavior
- **Learning**: Deepens understanding of Rust
- **Specialization**: Optimize for specific use cases

### 📝 Implementation Examples

#### Stack (LIFO)

```rust
#[derive(Debug)]
struct Stack<T> {
    elements: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self {
        Stack {
            elements: Vec::new(),
        }
    }
    
    fn with_capacity(capacity: usize) -> Self {
        Stack {
            elements: Vec::with_capacity(capacity),
        }
    }
    
    fn push(&mut self, item: T) {
        self.elements.push(item);
    }
    
    fn pop(&mut self) -> Option<T> {
        self.elements.pop()
    }
    
    fn peek(&self) -> Option<&T> {
        self.elements.last()
    }
    
    fn peek_mut(&mut self) -> Option<&mut T> {
        self.elements.last_mut()
    }
    
    fn len(&self) -> usize {
        self.elements.len()
    }
    
    fn is_empty(&self) -> bool {
        self.elements.is_empty()
    }
    
    fn clear(&mut self) {
        self.elements.clear();
    }
}

// Iterator implementation
impl<T> IntoIterator for Stack<T> {
    type Item = T;
    type IntoIter = std::vec::IntoIter<T>;
    
    fn into_iter(self) -> Self::IntoIter {
        self.elements.into_iter()
    }
}

// Usage
fn stack_example() {
    let mut stack = Stack::new();
    
    stack.push(1);
    stack.push(2);
    stack.push(3);
    
    println!("Stack: {:?}", stack);
    println!("Pop: {:?}", stack.pop()); // Some(3)
    println!("Peek: {:?}", stack.peek()); // Some(2)
    println!("Length: {}", stack.len()); // 2
    
    for item in stack {
        println!("Item: {}", item);
    }
}
```

#### Queue (FIFO)

```rust
use std::collections::VecDeque;

#[derive(Debug)]
struct Queue<T> {
    elements: VecDeque<T>,
}

impl<T> Queue<T> {
    fn new() -> Self {
        Queue {
            elements: VecDeque::new(),
        }
    }
    
    fn enqueue(&mut self, item: T) {
        self.elements.push_back(item);
    }
    
    fn dequeue(&mut self) -> Option<T> {
        self.elements.pop_front()
    }
    
    fn peek(&self) -> Option<&T> {
        self.elements.front()
    }
    
    fn len(&self) -> usize {
        self.elements.len()
    }
    
    fn is_empty(&self) -> bool {
        self.elements.is_empty()
    }
}
```

#### Linked List

```rust
#[derive(Debug)]
struct Node<T> {
    value: T,
    next: Option<Box<Node<T>>>,
}

#[derive(Debug)]
struct LinkedList<T> {
    head: Option<Box<Node<T>>>,
    size: usize,
}

impl<T> LinkedList<T> {
    fn new() -> Self {
        LinkedList {
            head: None,
            size: 0,
        }
    }
    
    fn push_front(&mut self, value: T) {
        let new_node = Box::new(Node {
            value,
            next: self.head.take(),
        });
        self.head = Some(new_node);
        self.size += 1;
    }
    
    fn pop_front(&mut self) -> Option<T> {
        self.head.take().map(|node| {
            self.head = node.next;
            self.size -= 1;
            node.value
        })
    }
    
    fn peek_front(&self) -> Option<&T> {
        self.head.as_ref().map(|node| &node.value)
    }
    
    fn len(&self) -> usize {
        self.size
    }
}
```

---

## 6️⃣ IMPLEMENTING METHODS

### 📖 What is it?

Advanced patterns for designing ergonomic APIs with builder patterns, fluent interfaces, and method chaining.

### 🎯 When to Use

- When constructing complex objects with many parameters
- For creating expressive, chainable APIs
- When you need compile-time validation

### 💡 Why Use These Patterns

- **Ergonomics**: Makes APIs easy to use and read
- **Flexibility**: Supports optional parameters
- **Safety**: Can enforce constraints at compile time

### 📝 Implementation Examples

#### Builder Pattern

```rust
#[derive(Debug)]
struct Server {
    host: String,
    port: u16,
    workers: usize,
    timeout: u64,
    ssl: bool,
}

struct ServerBuilder {
    host: Option<String>,
    port: Option<u16>,
    workers: usize,
    timeout: u64,
    ssl: bool,
}

impl ServerBuilder {
    fn new() -> Self {
        ServerBuilder {
            host: None,
            port: None,
            workers: 4,
            timeout: 30,
            ssl: false,
        }
    }
    
    fn host(mut self, host: &str) -> Self {
        self.host = Some(host.to_string());
        self
    }
    
    fn port(mut self, port: u16) -> Self {
        self.port = Some(port);
        self
    }
    
    fn workers(mut self, workers: usize) -> Self {
        self.workers = workers;
        self
    }
    
    fn build(self) -> Result<Server, String> {
        Ok(Server {
            host: self.host.ok_or("Host required")?,
            port: self.port.ok_or("Port required")?,
            workers: self.workers,
            timeout: self.timeout,
            ssl: self.ssl,
        })
    }
}

// Usage
let server = ServerBuilder::new()
    .host("localhost")
    .port(8080)
    .workers(8)
    .build()
    .unwrap();
```

#### Fluent Interface (Method Chaining)

```rust
#[derive(Debug)]
struct Calculator {
    value: i32,
}

impl Calculator {
    fn new() -> Self {
        Calculator { value: 0 }
    }
    
    fn add(mut self, x: i32) -> Self {
        self.value += x;
        self
    }
    
    fn multiply(mut self, x: i32) -> Self {
        self.value *= x;
        self
    }
    
    fn result(self) -> i32 {
        self.value
    }
}

// Usage
let result = Calculator::new()
    .add(5)
    .multiply(2)
    .result(); // 10
```

#### Type-State Pattern (Compile-time State)

```rust
struct Draft;
struct Published;

struct Document<State> {
    content: String,
    _state: std::marker::PhantomData<State>,
}

impl Document<Draft> {
    fn new() -> Self {
        Document {
            content: String::new(),
            _state: std::marker::PhantomData,
        }
    }
    
    fn add_text(&mut self, text: &str) {
        self.content.push_str(text);
    }
    
    fn publish(self) -> Document<Published> {
        Document {
            content: self.content,
            _state: std::marker::PhantomData,
        }
    }
}

impl Document<Published> {
    fn print(&self) {
        println!("{}", self.content);
    }
}

// Usage - compile-time state enforcement
let mut doc = Document::<Draft>::new();
doc.add_text("Content");
// doc.print(); // ❌ Compile error! Can't print draft
let doc = doc.publish();
doc.print(); // ✅ OK!
```

---

# 📚 WHAT WE'VE LEARNED, ACHIEVED & WHAT WE CAN NOW DO

## 🧠 Core Concepts Mastered

### 1. Collections & Data Organization
- **Vectors**: Dynamic arrays with powerful iteration and transformation methods
- **HashMaps**: Key-value storage with efficient O(1) lookups
- **Custom Structures**: Building specialized data structures (Stack, Queue, LinkedList)
- **Memory Layout**: Understanding heap vs. stack allocation and ownership

### 2. String Processing
- **String vs &str**: Owned vs. borrowed strings and when to use each
- **UTF-8 Safety**: Guaranteed valid Unicode with safe character handling
- **String Manipulation**: Splitting, searching, replacing, and transforming text
- **Efficient Concatenation**: Using builders and iterators for performance

### 3. Error Handling
- **Result<T, E>**: Handling fallible operations explicitly
- **Option<T>**: Representing optional values safely
- **? Operator**: Clean error propagation without nesting
- **Custom Errors**: Creating domain-specific error types

### 4. Design Patterns
- **Builder Pattern**: Constructing complex objects with optional parameters
- **Fluent Interfaces**: Method chaining for expressive APIs
- **Type-State Pattern**: Compile-time state validation
- **Iterator Adapters**: Composable transformations (map, filter, fold)

## 🎯 Practical Capabilities

✅ **Collection Mastery**
- Create and manipulate vectors with 20+ methods
- Use HashMaps for efficient data lookups and aggregation
- Build custom data structures from scratch

✅ **String Processing**
- Parse and validate text input
- Extract and transform data from strings
- Handle Unicode text safely

✅ **Error Management**
- Write functions that return meaningful errors
- Chain operations with the ? operator
- Create custom error types for libraries

✅ **API Design**
- Build ergonomic builder interfaces
- Create chainable method APIs
- Enforce constraints at compile time

## 🚀 Applications You Can Now Build

### 1. **Web Backend Services**
- Parse JSON requests → validate with custom errors
- Store user data in HashMaps/databases
- Process string input safely with UTF-8 validation
- Chain operations with iterators for data transformation

### 2. **CLI Tools**
- Parse command-line arguments into custom structures
- Use builder pattern for configuration
- Process text files line-by-line with iterators
- Handle errors gracefully with Result types

### 3. **Data Processing Pipeline**
- Load data from CSV using String splitting
- Store intermediate results in Vectors
- Use iterators to map and filter data
- Aggregate statistics with HashMaps (word counts, frequencies)

### 4. **File Management System**
- Read files and parse content with string methods
- Store file metadata in HashMaps
- Navigate directory structures
- Implement proper error handling for I/O operations

### 5. **Game Development**
- Use Vectors for entity management
- Store game state in custom structures (Stack for undo/redo)
- Parse game configuration files
- Use iterators for efficient game loop updates

### 6. **Caching/Memoization**
- Store computed results in HashMaps
- Use builder pattern for cache configuration
- Implement eviction policies with custom data structures
- Chain operations for efficient data access

### 7. **Text Processing Applications**
- Build search-and-replace utilities
- Parse structured text formats
- Validate user input with custom error types
- Process logs and generate reports

## 🔑 Five Fundamental Principles

### 1. **Ownership First**
All data structures you build must respect Rust's ownership rules. Vectors own their data, and you control when memory is freed.

### 2. **Error Propagation**
Use Result and Option everywhere fallible operations occur. Let callers decide how to handle errors—don't panic.

### 3. **Iterator Composition**
Build complex transformations by chaining small, focused iterator adapters (map, filter, fold). This is more efficient and readable.

### 4. **Type Safety**
Use custom types and builder patterns to encode invariants at compile time. If it compiles, it's likely correct.

### 5. **Zero-Cost Abstractions**
Rust's abstractions (generics, traits, iterators) compile to the same efficient code as manual loops. You don't pay for ergonomics.

## ✅ Phase 3 Complete Competency Checklist

- [ ] Create, push, pop, and access vectors efficiently
- [ ] Use Vec::with_capacity() for pre-allocation
- [ ] Chain iterator methods (map, filter, fold)
- [ ] Sort vectors with sort(), sort_by(), sort_by_key()
- [ ] Binary search on sorted vectors
- [ ] Create and manipulate HashMaps
- [ ] Use entry API for efficient updates
- [ ] Iterate over keys, values, and entries
- [ ] Create mutable strings and apply operations
- [ ] Parse strings with split() and split_whitespace()
- [ ] Search strings with find(), contains(), starts_with()
- [ ] Handle Result<T, E> with match and ? operator
- [ ] Handle Option<T> with match and unwrap_or()
- [ ] Create custom error types with Display and Error traits
- [ ] Build Stack and Queue data structures
- [ ] Implement iterators for custom types
- [ ] Use builder pattern for complex objects
- [ ] Chain methods with fluent interfaces
- [ ] Implement type-state pattern for state machines
- [ ] Combine Result and Option in complex scenarios

---

**🎓 You've completed Phase 3! You now have strong fundamentals in Rust collections, strings, error handling, and design patterns. You're ready to build real applications and tackle advanced topics like concurrency, traits, and async/await. 🦀**


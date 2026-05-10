# Rust Programming Reference

**Focus**: Memory-safe systems programming, ownership model, zero-cost abstractions, concurrency, and performance-critical code without garbage collection.

## Table of Contents

- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Ownership & Borrowing](#ownership--borrowing)
- [Lifetimes](#lifetimes)
- [Operators](#operators)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Structs & Enums](#structs--enums)
- [Pattern Matching](#pattern-matching)
- [Traits](#traits)
- [Generics](#generics)
- [Error Handling](#error-handling)
- [Collections](#collections)
- [Smart Pointers](#smart-pointers)
- [Concurrency](#concurrency)
- [Common Patterns](#common-patterns)

---

## Built-In Syntax

**Keywords**: `let` (immutable binding), `mut` (mutable), `const` (compile-time constant), `static` (global), `fn` (function), `struct`, `enum`, `impl` (implementation), `trait` (interface-like), `mod` (module), `use` (import), `pub` (public), `match` (pattern matching), `if let` / `while let`, `loop`, `break` / `continue`, `return` (optional)

**Access modifiers**: `pub` (public), (no modifier) - private (default)

---

## Variables & Basic Types

**Primitive types**: `i8`, `i16`, `i32`, `i64`, `i128`, `isize` (signed); `u8`, `u16`, `u32`, `u64`, `u128`, `usize` (unsigned); `f32`, `f64` (floating); `bool`; `char` (4-byte Unicode); `()` (unit type)

**Type inference**: `let x = 42;` (i32 inferred), `let y: u64 = 100;` (explicit), `let mut z = 10;` (mutable)

**Shadowing**: `let x = 5; let x = x + 1;` - Can shadow with different type

**Memory Model**: No garbage collection. Stack for values, heap via `Box<T>` or collections. Ownership system ensures memory safety at compile time - no use-after-free, double-free, or data races.

---

## Ownership & Borrowing

**Core concept**: Rust's ownership system ensures memory safety without garbage collection.

**Ownership rules**: Each value has one owner, only one owner at a time, value dropped when owner goes out of scope

```rust
let s1 = String::from("hello");
let s2 = s1;  // s1 moved to s2, s1 no longer valid
```

**Borrowing (references)**:
- **Immutable borrow** (`&T`): `let len = calculate_length(&s);` - Multiple allowed simultaneously
- **Mutable borrow** (`&mut T`): `change_string(&mut s);` - Only one at a time, exclusive

**Borrowing rules**: Multiple `&T` allowed, only one `&mut T`, cannot mix, references must be valid (lifetime)

**Dereferencing**: `*ref` to get value, automatic in most cases (method calls)

---

## Lifetimes

**Lifetime annotations**: Ensure references are valid
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

**Lifetime elision**: Compiler infers lifetimes in common cases: `fn first_word(s: &str) -> &str { }`

**Static lifetime**: `'static` - lives for entire program: `let s: &'static str = "hello";`

**Best practices**: Let compiler infer when possible, use explicit lifetimes for complex cases

---

## Operators

**Arithmetic**: `+ - * / %` (checked: `checked_add()`, `checked_mul()`)

**Comparison**: `== != < > <= >=`

**Logical**: `&& || !`

**Bitwise**: `& | ^ ! << >>`

**Assignment**: `= += -= *= /= %=`

**Dereference**: `*` (explicit), automatic for method calls

**Borrow**: `&` (immutable), `&mut` (mutable)

**Range**: `..` (exclusive), `..=` (inclusive): `0..10`, `0..=10`

---

## Control Flow

**If/Else**: `if condition { } else if { } else { }`

**If as expression**: `let number = if condition { 5 } else { 6 };`

**Loop** (infinite): `loop { if condition { break; } }`

**While**: `while condition { }`

**For** (iterator): `for item in vec.iter() { }`, `for (i, v) in vec.iter().enumerate() { }`, `for i in 0..10 { }`

**Match** (pattern matching):
```rust
match value {
    1 => println!("one"),
    2 | 3 => println!("two or three"),
    4..=10 => println!("four to ten"),
    _ => println!("other"),
}
```

**If let / While let**: `if let Some(value) = option { }`, `while let Some(item) = stack.pop() { }`

---

## Functions

**Definition**: `fn add(x: i32, y: i32) -> i32 { x + y }` - Last expression is return (no semicolon)

**Parameters**: Pass by value (moves or copies), use `&` for borrowing

**Return**: Last expression (no semicolon) or `return` keyword

**Diverging functions**: `fn never_returns() -> ! { loop {} }` - never returns

**Closures** (anonymous functions):
```rust
let add = |x, y| x + y;
let closure = || x + 1;  // borrows x
let closure = move || x + 1;  // moves x
```

---

## Structs & Enums

**Structs**:
```rust
struct Point { x: i32, y: i32 }
let p = Point { x: 0, y: 0 };
let p = Point { x: 0, ..default };  // struct update

// Tuple struct
struct Color(u8, u8, u8);

// Unit struct
struct Marker;
```

**Methods**:
```rust
impl Point {
    fn new(x: i32, y: i32) -> Point { Point { x, y } }
    fn distance(&self) -> f64 { /* ... */ }  // immutable borrow
    fn move_by(&mut self, dx: i32, dy: i32) { /* ... */ }  // mutable borrow
}
```

**Enums**:
```rust
enum Option<T> { Some(T), None }
enum Result<T, E> { Ok(T), Err(E) }
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}
```

**No null**: Rust uses `Option<T>` instead of null pointers - `Some(value)` or `None`

---

## Pattern Matching

**Match expression**:
```rust
match value {
    Pattern1 => expression1,
    Pattern2 if condition => expression2,
    Pattern3 | Pattern4 => expression3,
    _ => default,
}
```

**Destructuring**: `let Point { x, y } = point;`, `let (a, b) = tuple;`, `if let Some(value) = option { }`

**Pattern guards**: `match x { Some(n) if n > 10 => ..., _ => ... }`

**Binding**: `match x { Some(n @ 1..=10) => ..., _ => ... }`

---

## Traits

**Trait definition** (similar to interfaces):
```rust
trait Draw {
    fn draw(&self);
    fn area(&self) -> f64 { 0.0 }  // default implementation
}
```

**Implementation**: `impl Draw for Circle { fn draw(&self) { } }`

**Trait bounds** (generics): `fn print<T: Display>(item: T) { }` or `fn print<T>(item: T) where T: Display { }`

**Common traits**: `Clone`, `Copy`, `Display`, `Debug`, `PartialEq`, `Eq`, `PartialOrd`, `Ord`, `Hash`, `Default`, `Iterator`

**Derive macros**: `#[derive(Debug, Clone, PartialEq)]` - auto-generate trait implementations

---

## Generics

**Function generics**:
```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list { if item > largest { largest = item; } }
    largest
}
```

**Struct generics**:
```rust
struct Point<T> { x: T, y: T }
impl<T> Point<T> {
    fn new(x: T, y: T) -> Point<T> { Point { x, y } }
}
```

**Enum generics**: `Option<T>`, `Result<T, E>`

**Trait bounds**: `T: Display + Clone`, `T: Iterator<Item = i32>`

---

## Error Handling

**Result<T, E>**: `Ok(value)` for success, `Err(error)` for failure
```rust
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 { Err(String::from("Division by zero")) } else { Ok(a / b) }
}
```

**Handling errors**:
```rust
match result {
    Ok(value) => println!("{}", value),
    Err(e) => println!("Error: {}", e),
}
// Unwrap: result.unwrap(), result.expect("message")
// Default: result.unwrap_or(0)
// Propagate: result?  // early return on error
```

**Option<T>**: `Some(value)` or `None`
```rust
let value = option.unwrap_or(0);
let value = option.unwrap_or_else(|| calculate_default());
if let Some(value) = option { }
```

**Panic**: `panic!("message")` - unrecoverable error, terminates program

**Best practices**: Use `Result` for recoverable errors, `Option` for optional values, propagate with `?`, avoid `unwrap()` in production code

---

## Collections

**Vector** (`Vec<T>`):
```rust
let mut vec = Vec::new();
vec.push(1);
let vec = vec![1, 2, 3];  // macro
let first = vec[0];  // panic if out of bounds
let first = vec.get(0);  // returns Option<&T>
```

**String** (`String`):
```rust
let mut s = String::from("hello");
s.push_str(" world");
let s = format!("Hello, {}!", name);
let slice: &str = &s[0..5];  // string slice
```

**HashMap** (`HashMap<K, V>`):
```rust
use std::collections::HashMap;
let mut map = HashMap::new();
map.insert("key", "value");
let value = map.get("key");  // returns Option<&V>
```

**HashSet** (`HashSet<T>`):
```rust
use std::collections::HashSet;
let mut set = HashSet::new();
set.insert(1);
set.contains(&1);
```

**Iterators**: `vec.iter()` (borrow), `vec.into_iter()` (move), `&vec` (borrow sugar)

**Iterator methods**: `map()`, `filter()`, `collect()`, `fold()`, `sum()`, `find()`, `any()`, `all()`

---

## Smart Pointers

**Box<T>**: Heap allocation - `let b = Box::new(5);`

**Rc<T>**: Reference counted (shared ownership, immutable)
```rust
use std::rc::Rc;
let a = Rc::new(5);
let b = Rc::clone(&a);  // shared ownership
```

**RefCell<T>**: Interior mutability (runtime borrow checking)
```rust
use std::cell::RefCell;
let data = RefCell::new(5);
let mut borrow = data.borrow_mut();  // runtime check
```

**Arc<T>**: Atomic reference counted (thread-safe) - `use std::sync::Arc;`

**Best practices**: Use `Box` for heap allocation, `Rc`/`Arc` for shared ownership, `RefCell` for interior mutability, prefer ownership/borrowing when possible

---

## Concurrency

**Threads**:
```rust
use std::thread;
let handle = thread::spawn(|| { /* thread code */ });
handle.join().unwrap();
```

**Message passing** (channels):
```rust
use std::sync::mpsc;
let (tx, rx) = mpsc::channel();
tx.send(value).unwrap();
let received = rx.recv().unwrap();
```

**Shared state** (Mutex):
```rust
use std::sync::{Arc, Mutex};
let data = Arc::new(Mutex::new(0));
let data_clone = Arc::clone(&data);
let mut num = data_clone.lock().unwrap();
*num += 1;
```

**Async/await** (requires `async-std` or `tokio`):
```rust
async fn fetch_data() -> Result<String, Error> { /* async code */ }
let result = fetch_data().await;
```

**Best practices**: Prefer message passing over shared state, use `Arc<Mutex<T>>` for shared mutable state, leverage Rust's ownership for thread safety

---

## Common Patterns

### Builder Pattern
```rust
impl Config {
    fn new() -> Config { Config { host: String::new(), port: 8080 } }
    fn host(mut self, host: String) -> Self { self.host = host; self }
    fn port(mut self, port: u16) -> Self { self.port = port; self }
}
```

### Iterator Pattern
```rust
let sum: i32 = vec.iter()
    .filter(|x| x % 2 == 0)
    .map(|x| x * 2)
    .sum();
```

### Error Propagation
```rust
fn read_file(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?;  // ? propagates error
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

### Option Chaining
```rust
let result = option
    .map(|x| x * 2)
    .filter(|x| x > 10)
    .unwrap_or(0);
```

---

## Memory Safety Guarantees

**Compile-time guarantees** (no runtime overhead):
- **No null pointer dereferences**: Use `Option<T>` instead of null
- **No use-after-free**: Ownership system prevents accessing freed memory
- **No double-free**: Only one owner, automatic cleanup
- **No data races**: Ownership + borrowing rules prevent concurrent access issues
- **No buffer overflows**: Bounds checking (panic) or safe APIs

**Trade-offs**: More compile-time checks, sometimes need to restructure code to satisfy borrow checker, learning curve for ownership model

**Best practices**: Embrace ownership model, use `Option`/`Result` for error handling, leverage compiler errors to learn, prefer borrowing over cloning when possible

---

> **Note**: Rust provides memory safety without garbage collection through ownership, borrowing, and lifetimes. The compiler enforces these rules at compile time, preventing common memory errors. Rust is ideal for systems programming, performance-critical code, and concurrent applications where safety and performance are both important.

# C++ Programming Reference

**Focus**: Object-oriented systems programming, RAII, templates, STL, modern C++ features, performance-critical code, and system integration.

## Table of Contents

- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [References vs Pointers](#references-vs-pointers)
- [Arrays & Strings](#arrays--strings)
- [Classes & Objects](#classes--objects)
- [Templates](#templates)
- [STL Containers](#stl-containers)
- [Smart Pointers](#smart-pointers)
- [RAII & Resource Management](#raii--resource-management)
- [Exception Handling](#exception-handling)
- [File I/O](#file-io)
- [Memory Management](#memory-management)
- [Common Patterns](#common-patterns)

---

## Built-In Syntax

### Type modifiers
- `const` - read-only variable/method
- `volatile` - value may change unexpectedly
- `static` - class member or file scope
- `extern` - external linkage
- `mutable` - can be modified in const methods
- `auto` - type inference (C++11+)
- `decltype` - get type of expression (C++11+)

### Access modifiers
- `public` - accessible from anywhere
- `private` - accessible only within class
- `protected` - accessible within class and derived classes

### Class keywords
- `class` - define class (default private)
- `struct` - define struct (default public, can have methods)
- `union` - shared memory type
- `enum` / `enum class` (C++11+) - enumeration
- `namespace` - namespace scope

### Control flow
- `if`, `else`, `switch`, `case`, `default`
- `for`, `while`, `do-while`
- `break`, `continue`
- `return`
- Range-based for (C++11+): `for (auto& item : container)`

---

## Variables & Basic Types

**Same as C**: `int`, `short`, `long`, `long long`, `float`, `double`, `char`, `bool`

**C++ additions**:
- `bool` - native boolean type (`true`/`false`)
- `wchar_t` - wide character
- `char16_t`, `char32_t` (C++11+) - Unicode characters
- `nullptr` (C++11+) - null pointer literal (use instead of `NULL`)

**Type inference** (C++11+):
- `auto x = 42;` - compiler infers type
- `decltype(expr)` - get type of expression
- `auto& ref = var;` - reference type inference

**Memory Model**: C++ supports both stack (automatic) and heap (manual) allocation like C, but adds RAII for automatic resource management. Stack variables destroyed when scope ends; heap requires `new`/`delete` or smart pointers.

---

## Operators

**Same as C**: Arithmetic, comparison, logical, bitwise, assignment, address/dereference

**C++ additions**:
- `::` - scope resolution operator
- `new` / `delete` - dynamic allocation
- `->` - member access via pointer (also for smart pointers)
- `.*` / `->*` - pointer-to-member access
- `throw` - throw exception
- `try` / `catch` - exception handling
- `typeid` - runtime type information

**Operator overloading**: Can overload `+`, `-`, `*`, `/`, `==`, `!=`, `[]`, `()`, etc. for custom types

---

## Control Flow

**Same as C**: `if/else`, `switch`, `for`, `while`, `do-while`

**C++ additions**:
- **Range-based for** (C++11+): `for (auto& item : vec) { }` - iterate over containers
- **Initializer in if** (C++17+): `if (auto result = getValue(); result > 0) { }`
- **Structured bindings** (C++17+): `auto [x, y] = getPoint();`

---

## Functions

**Same as C**: Function definitions, parameters, return types

**C++ additions**:
- **Default arguments**: `void func(int x = 10);`
- **Function overloading**: Same name, different parameters
- **Inline functions**: `inline int add(int a, int b) { return a + b; }`
- **Lambda expressions** (C++11+): `auto f = [](int x) { return x * 2; };`
- **Function templates**: `template<typename T> T max(T a, T b) { return a > b ? a : b; }`
- **Member functions**: Functions inside classes
- **Const member functions**: `int getValue() const;` - doesn't modify object

**Lambda syntax** (C++11+):
```cpp
auto lambda = [capture](params) -> return_type { body };
// [=] capture by value, [&] capture by reference, [x, &y] mixed
```

---

## References vs Pointers

**References** (C++ addition):
- **Declaration**: `int& ref = var;` - Must be initialized, cannot be reassigned
- **Usage**: `ref = 20;` (no `*` needed) - Acts like alias to variable
- **Function params**: `void func(int& x)` - Pass by reference (modifies caller's variable)
- **Return**: `int& getRef()` - Return reference (be careful with lifetime)

**Pointers** (from C):
- **Declaration**: `int* ptr = &var;` - Can be reassigned, can be NULL
- **Usage**: `*ptr = 20;` - Requires dereference
- **Function params**: `void func(int* x)` - Pass by pointer

**When to use**:
- **References**: Function parameters (prefer over pointers), return values, aliases
- **Pointers**: Optional/nullable values, dynamic allocation, arrays, low-level operations

**Key differences**: References cannot be NULL, cannot be reassigned, no pointer arithmetic, safer but less flexible

---

## Arrays & Strings

### Arrays

**Same as C**: Fixed-size arrays, array initialization, array decay to pointers

**C++ additions**:
- **std::array** (C++11+): `std::array<int, 10> arr;` - Fixed-size, bounds-checked (optional), no decay
- **Range-based for**: `for (auto& item : arr) { }`

### Strings

**C-style**: `char str[] = "Hello";` (same as C)

**C++ strings** (`<string>`):
- **std::string**: `std::string str = "Hello";` - Dynamic size, automatic memory management
- **Operations**: `str.length()`, `str += " World"`, `str.find("lo")`, `str.substr(0, 5)`
- **Conversion**: `std::to_string(42)`, `std::stoi("123")`

**String literals** (C++11+): `auto str = "Hello"s;` (suffix makes std::string)

---

## Classes & Objects

### Class Definition

```cpp
class MyClass {
private:
    int value;  // private by default in class
    
public:
    // Constructor
    MyClass(int v) : value(v) { }  // member initializer list
    
    // Destructor
    ~MyClass() { }  // cleanup
    
    // Member function
    int getValue() const { return value; }
    
    // Static member
    static int count;
};
```

### Constructors & Destructors

- **Constructor**: `MyClass(int x) : member(x) { }` - Initialize object, member initializer list
- **Default constructor**: `MyClass() = default;` (C++11+)
- **Copy constructor**: `MyClass(const MyClass& other);`
- **Move constructor** (C++11+): `MyClass(MyClass&& other) noexcept;`
- **Destructor**: `~MyClass() { }` - Cleanup when object destroyed
- **Member initializer list**: `: member1(val1), member2(val2)` - Initialize before body

### Inheritance

```cpp
class Base {
public:
    virtual void func() { }  // virtual for polymorphism
};

class Derived : public Base {
public:
    void func() override { }  // override (C++11+)
};
```

- **Access specifiers**: `public`, `protected`, `private` inheritance
- **Virtual functions**: `virtual` enables polymorphism, `override` (C++11+) ensures override
- **Abstract class**: `virtual void func() = 0;` - pure virtual, cannot instantiate
- **Multiple inheritance**: `class D : public B1, public B2 { }` - Inherit from multiple bases

### Operator Overloading

```cpp
class Vector {
    Vector operator+(const Vector& other) const {
        return Vector(x + other.x, y + other.y);
    }
    Vector& operator=(const Vector& other) {  // assignment
        if (this != &other) { /* copy */ }
        return *this;
    }
    int& operator[](int index) { return data[index]; }
};
```

### Special Member Functions

- **Rule of Three/Five**: If you define one of: destructor, copy constructor, copy assignment, you should consider all
- **Rule of Zero** (C++11+): Use smart pointers/RAII, let compiler generate defaults
- **Move semantics** (C++11+): `std::move()` transfers ownership, `&&` rvalue reference

---

## Templates

**Function templates**:
```cpp
template<typename T>
T max(T a, T b) {
    return a > b ? a : b;
}
// Usage: max(3, 5) or max<int>(3, 5)
```

**Class templates**:
```cpp
template<typename T>
class Stack {
private:
    std::vector<T> data;
public:
    void push(const T& item) { data.push_back(item); }
    T pop() { /* ... */ }
};
// Usage: Stack<int> stack;
```

**Template parameters**:
- `template<typename T>` or `template<class T>` (equivalent)
- `template<int N>` - non-type parameter
- `template<typename T = int>` - default template argument

**Concepts** (C++20): `template<std::integral T>` - Constrain template parameters

---

## STL Containers

**Sequence containers** (`<vector>`, `<deque>`, `<list>`, `<array>`):
- **std::vector**: `std::vector<int> vec;` - Dynamic array, `vec.push_back(1)`, `vec[0]`, `vec.size()`
- **std::array** (C++11+): `std::array<int, 10> arr;` - Fixed-size array
- **std::deque**: Double-ended queue
- **std::list**: Doubly-linked list

**Associative containers** (`<map>`, `<set>`, `<unordered_map>`, `<unordered_set>`):
- **std::map**: `std::map<std::string, int> m;` - Sorted key-value, `m["key"] = 10`, `m.find("key")`
- **std::unordered_map** (C++11+): Hash map, faster lookup, unsorted
- **std::set**: Sorted unique values
- **std::unordered_set** (C++11+): Hash set

**Container adapters** (`<stack>`, `<queue>`, `<priority_queue>`):
- **std::stack**: `std::stack<int> s; s.push(1); s.pop();`
- **std::queue**: FIFO queue
- **std::priority_queue**: Max heap

**Iterators**: `vec.begin()`, `vec.end()`, `for (auto it = vec.begin(); it != vec.end(); ++it)`

**Algorithms** (`<algorithm>`): `std::sort()`, `std::find()`, `std::count()`, `std::transform()`, `std::for_each()`

---

## Smart Pointers

**RAII-based automatic memory management** (C++11+):

- **std::unique_ptr**: `std::unique_ptr<int> ptr(new int(42));` - Exclusive ownership, cannot copy, can move
- **std::shared_ptr**: `std::shared_ptr<int> ptr = std::make_shared<int>(42);` - Shared ownership, reference counted
- **std::weak_ptr**: `std::weak_ptr<int> wptr = shared_ptr;` - Non-owning reference, breaks cycles

**Usage**:
```cpp
// Prefer make_unique/make_shared (exception-safe)
auto ptr = std::make_unique<MyClass>(args);
auto shared = std::make_shared<MyClass>(args);

// Automatic cleanup when out of scope
// No manual delete needed
```

**Best practices**: Prefer smart pointers over raw `new`/`delete`, use `make_unique`/`make_shared`, `unique_ptr` for single ownership, `shared_ptr` for shared ownership

---

## RAII & Resource Management

**RAII** (Resource Acquisition Is Initialization): Resources acquired in constructor, released in destructor

**Benefits**: Automatic cleanup, exception-safe, no manual resource management

**Examples**:
- **File handling**: `std::fstream` - automatically closes file
- **Locks**: `std::lock_guard<std::mutex>` - automatically unlocks
- **Smart pointers**: Automatically delete when out of scope
- **Custom RAII**: Destructor handles cleanup

**Pattern**: Acquire resource in constructor, release in destructor, use stack allocation or smart pointers

---

## Exception Handling

**Exception syntax**:
```cpp
try {
    riskyFunction();
} catch (const std::exception& e) {
    std::cerr << e.what() << std::endl;
} catch (...) {
    // catch all
}
```

**Throwing**: `throw std::runtime_error("Error message");`

**Exception types**: `std::exception` (base), `std::runtime_error`, `std::logic_error`, `std::bad_alloc`

**RAII + exceptions**: Destructors called during stack unwinding, ensures cleanup even if exception thrown

**Best practices**: Throw by value, catch by const reference, use RAII for exception safety, don't throw from destructors

---

## File I/O

**C-style**: Same as C (`<cstdio>`): `fopen()`, `fprintf()`, `fclose()`

**C++ streams** (`<fstream>`):
```cpp
// Write
std::ofstream file("output.txt");
file << "Hello" << std::endl;
file.close();

// Read
std::ifstream file("input.txt");
std::string line;
while (std::getline(file, line)) {
    // process line
}
```

**Stream operators**: `<<` (output), `>>` (input), `std::endl` (newline + flush)

**RAII**: File streams automatically close in destructor

---

## Memory Management

**C-style**: `malloc()`/`free()` (from C, avoid in C++), `new`/`delete` (C++)

**new/delete**:
```cpp
int* ptr = new int(42);  // allocate and initialize
delete ptr;  // free memory
ptr = nullptr;  // prevent dangling pointer

int* arr = new int[10];  // array
delete[] arr;  // array delete
```

**Best practices**: **Prefer smart pointers** over `new`/`delete`, use `make_unique`/`make_shared`, match `new` with `delete`, `new[]` with `delete[]`

**Common pitfalls**: Memory leaks (forgot delete), use-after-free, double-delete, mismatched new/delete (new vs new[])

**Modern C++**: Use smart pointers, avoid raw `new`/`delete` in application code

---

## Common Patterns

### RAII Pattern
```cpp
class FileHandle {
    FILE* file;
public:
    FileHandle(const char* name) : file(fopen(name, "r")) {
        if (!file) throw std::runtime_error("Cannot open file");
    }
    ~FileHandle() { if (file) fclose(file); }
    // Delete copy constructor/assignment (or use unique_ptr)
};
```

### Singleton Pattern
```cpp
class Singleton {
private:
    static Singleton* instance;
    Singleton() { }
public:
    static Singleton* getInstance() {
        if (!instance) instance = new Singleton();
        return instance;
    }
};
```

### Factory Pattern
```cpp
class Factory {
public:
    static std::unique_ptr<Base> create(const std::string& type) {
        if (type == "A") return std::make_unique<DerivedA>();
        return std::make_unique<DerivedB>();
    }
};
```

### Iterator Pattern
```cpp
for (auto it = vec.begin(); it != vec.end(); ++it) {
    // *it to access element
}
// Or range-based for (preferred)
for (auto& item : vec) { }
```

---

## Undefined Behavior

**Same as C**: Dereferencing NULL, buffer overflow, use-after-free, uninitialized variables, etc.

**C++ additions**:
- **Dereferencing null pointer**: `int* p = nullptr; *p = 10;` - Crash
- **Accessing destroyed object**: Using reference/pointer to out-of-scope object
- **Double delete**: `delete ptr; delete ptr;` - Undefined behavior
- **Virtual function call in destructor**: Calling virtual function during destruction
- **Modifying const object**: Through const_cast (undefined if object is actually const)

**No runtime checks**: C++ trusts the programmer like C, but RAII and smart pointers help prevent many issues

---

> **Note**: C++ is a multi-paradigm language (procedural, OOP, generic) with manual memory management but RAII for automatic resource cleanup. Prefer smart pointers over raw pointers, use STL containers, leverage RAII for exception safety. Modern C++ (C++11+) adds many safety and convenience features. For automatic garbage collection, consider managed languages like C# or Java.


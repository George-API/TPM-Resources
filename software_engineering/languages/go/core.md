# Go Syntax Reference

**Focus**: Tight syntax reference for Go - keywords, operators, data types, control flow, functions, structs, interfaces, concurrency, error handling, I/O.

## Table of Contents

- [Environment Setup](#environment-setup)
- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Strings](#strings)
- [Arrays & Slices](#arrays--slices)
- [Maps](#maps)
- [Structs](#structs)
- [Interfaces](#interfaces)
- [Pointers](#pointers)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Error Handling](#error-handling)
- [Concurrency](#concurrency)
- [File I/O](#file-io)
- [Packages & Modules](#packages--modules)
- [Common Built-in Functions](#common-built-in-functions)

---

## Environment Setup

- Install Go: `https://golang.org/dl/`
- Create module: `go mod init github.com/user/repo`
- Add dependency: `go get package@version`
- Build: `go build`
- Run: `go run main.go`
- Test: `go test`
- Format: `go fmt`
- Install binary: `go install`

---

## Built-In Syntax

### Values
- `true` - Boolean true value
- `false` - Boolean false value
- `nil` - Zero value for pointers, interfaces, slices, maps, channels, functions

### Type declarations
- `var` - Declare variable
- `const` - Declare constant
- `type` - Declare type alias or new type
- `func` - Declare function

### Control flow
- `if` - Conditional branch
- `else` - Default branch
- `switch` - Multi-way branch
- `case` - Switch case
- `default` - Default case
- `for` - Loop (only looping construct)
- `range` - Iterate over collections
- `break` - Exit loop/switch
- `continue` - Skip to next iteration
- `goto` - Jump to label (rarely used)

### Functions & methods
- `func` - Define function
- `return` - Return value(s)
- `defer` - Defer function execution (cleanup)
- `go` - Start goroutine (concurrency)

### Error handling
- `panic` - Trigger runtime panic
- `recover` - Recover from panic

### Packages & imports
- `package` - Declare package
- `import` - Import package
- `_` - Blank identifier (ignore value)

### Concurrency
- `chan` - Channel type
- `select` - Choose from multiple channels
- `go` - Start goroutine

### Memory management
- `new()` - Allocate zeroed value, return pointer
- `make()` - Allocate and initialize slice, map, or channel

---

## Variables & Basic Types

### Variable Declaration
- `var x int = 10` - Explicit type
- `var x = 10` - Type inference
- `x := 10` - Short declaration (inside functions)
- `var x, y int = 10, 20` - Multiple variables
- `x, y := 10, 20` - Multiple short declaration

### Basic Types
- `bool` - Boolean (true/false)
- `string` - String (immutable)
- `int`, `int8`, `int16`, `int32`, `int64` - Signed integers
- `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr` - Unsigned integers
- `byte` - Alias for uint8
- `rune` - Alias for int32 (Unicode code point)
- `float32`, `float64` - Floating point
- `complex64`, `complex128` - Complex numbers

### Type Conversion
- `int(x)` - Convert to int
- `float64(x)` - Convert to float64
- `string(x)` - Convert to string
- `[]byte(s)` - Convert string to byte slice

### Constants
- `const Pi = 3.14` - Untyped constant
- `const Pi float64 = 3.14` - Typed constant
- `const (a = 1; b = 2)` - Multiple constants
- `const (a = iota; b; c)` - Iota (enum-like)

### Zero Values
- `0` for numeric types
- `false` for bool
- `""` for string
- `nil` for pointers, slices, maps, channels, interfaces, functions

---

## Operators

### Arithmetic
- `+` - add
- `-` - subtract
- `*` - multiply
- `/` - divide
- `%` - modulo
- `++` - increment (statement, not expression)
- `--` - decrement (statement, not expression)

### Comparison
- `==` - equality
- `!=` - inequality
- `<`, `>`, `<=`, `>=` - relational operators

### Logical
- `&&` - logical AND
- `||` - logical OR
- `!` - logical NOT

### Bitwise
- `&` - bitwise AND
- `|` - bitwise OR
- `^` - bitwise XOR
- `&^` - bit clear (AND NOT)
- `<<` - left shift
- `>>` - right shift

### Address & Dereference
- `&x` - address of x (pointer)
- `*p` - value pointed to by p (dereference)

### Channel
- `<-ch` - receive from channel
- `ch <- value` - send to channel

### Assignment
- `=` - assign
- `+=`, `-=`, `*=`, `/=`, `%=` - compound assignment
- `&=`, `|=`, `^=`, `<<=`, `>>=` - bitwise compound assignment

---

## Strings

### String Creation
- `s := "hello"` - Double quotes (interpreted)
- `s := 'h'` - Single quotes (rune)
- `` s := `multiline\nstring` `` - Backticks (raw string, no escaping)

### String Operations
- `s1 + s2` - Concatenate
- `len(s)` - Length in bytes
- `s[i]` - Get byte at index
- `s[i:j]` - Slice substring
- `strings.Contains(s, "sub")` - Check substring
- `strings.HasPrefix(s, "pre")` - Check prefix
- `strings.HasSuffix(s, "suf")` - Check suffix
- `strings.Index(s, "sub")` - Find index
- `strings.Replace(s, "old", "new", n)` - Replace (n = -1 for all)
- `strings.Split(s, ",")` - Split into slice
- `strings.Join(slice, ",")` - Join slice
- `strings.ToUpper(s)`, `strings.ToLower(s)` - Case conversion
- `strings.TrimSpace(s)` - Remove whitespace
- `strconv.Atoi(s)` - String to int
- `strconv.Itoa(i)` - Int to string

### String Formatting
- `fmt.Sprintf("Name: %s, Age: %d", name, age)` - Format string
- `fmt.Printf("Value: %v\n", x)` - Print formatted
- `%v` - Default format
- `%+v` - Include struct fields
- `%#v` - Go syntax representation
- `%T` - Type
- `%d` - Integer
- `%f` - Float
- `%s` - String
- `%t` - Boolean
- `%p` - Pointer

---

## Arrays & Slices

### Arrays (Fixed Size)
- `var arr [5]int` - Array of 5 ints
- `arr := [5]int{1, 2, 3, 4, 5}` - Initialize
- `arr := [...]int{1, 2, 3}` - Size inferred
- `len(arr)` - Length
- `arr[i]` - Access element
- `arr[i:j]` - Slice (creates slice, not array)

### Slices (Dynamic)
- `var s []int` - Declare slice
- `s := []int{1, 2, 3}` - Initialize
- `s := make([]int, 5)` - Make slice (length 5)
- `s := make([]int, 5, 10)` - Make slice (length 5, capacity 10)
- `s := arr[1:3]` - Slice from array
- `len(s)` - Length
- `cap(s)` - Capacity
- `s[i]` - Access element
- `s[i:j]` - Subslice
- `s = append(s, 4)` - Append element
- `s = append(s, 4, 5, 6)` - Append multiple
- `s = append(s, s2...)` - Append slice
- `copy(dst, src)` - Copy slice
- `s = s[1:]` - Remove first element
- `s = s[:len(s)-1]` - Remove last element

### Slice Operations
- `s[:]` - Full slice
- `s[1:]` - From index 1
- `s[:3]` - Up to index 3
- `s[1:3]` - Range [1:3)

---

## Maps

### Creation
- `var m map[string]int` - Declare map
- `m := make(map[string]int)` - Make map
- `m := map[string]int{"a": 1, "b": 2}` - Initialize
- `m := map[string]int{}` - Empty map

### Access & Modify
- `m["key"]` - Get value (returns zero value if missing)
- `value, ok := m["key"]` - Get with existence check
- `m["key"] = 3` - Set value
- `delete(m, "key")` - Delete key
- `len(m)` - Number of key-value pairs

### Iteration
- `for k, v := range m { ... }` - Iterate with key and value
- `for k := range m { ... }` - Iterate keys only

---

## Structs

### Definition
- `type Person struct { Name string; Age int }` - Define struct
- `type Person struct { Name string; Age int; Email string }` - Multiple fields
- `type Person struct { name string; age int }` - Unexported fields (lowercase)

### Instantiation
- `p := Person{Name: "Alice", Age: 30}` - Initialize with field names
- `p := Person{"Alice", 30}` - Initialize by position
- `p := Person{}` - Zero value
- `p := &Person{Name: "Alice"}` - Pointer to struct
- `p := new(Person)` - New pointer (zero value)

### Access
- `p.Name` - Access field
- `p.Age = 31` - Modify field
- `(*p).Name` - Dereference pointer (Go auto-dereferences)
- `p.Name` - Direct access (Go handles pointer)

### Embedded Structs
- `type Employee struct { Person; ID int }` - Embed Person
- `e.Name` - Access embedded field directly
- `e.Person.Name` - Explicit access

### Methods
- `func (p Person) String() string { return p.Name }` - Value receiver
- `func (p *Person) SetAge(age int) { p.Age = age }` - Pointer receiver
- `p.String()` - Call method
- `p.SetAge(31)` - Call pointer method (Go handles)

---

## Interfaces

### Definition
- `type Writer interface { Write([]byte) (int, error) }` - Define interface
- `type Reader interface { Read([]byte) (int, error) }` - Single method
- `type ReadWriter interface { Reader; Writer }` - Embed interfaces

### Implementation
- Any type with matching methods implements interface (implicit)
- `var w Writer = os.Stdout` - Assign compatible type
- `w.Write([]byte("hello"))` - Call interface method

### Type Assertion
- `v, ok := i.(string)` - Assert type (safe)
- `v := i.(string)` - Assert type (panics if wrong)
- `switch v := i.(type) { case string: ... }` - Type switch

### Common Interfaces
- `error` - Error interface: `Error() string`
- `io.Reader` - Read data
- `io.Writer` - Write data
- `io.Closer` - Close resource
- `fmt.Stringer` - String representation: `String() string`

---

## Pointers

### Declaration
- `var p *int` - Pointer to int
- `p := &x` - Address of x
- `*p` - Dereference pointer
- `p = nil` - Nil pointer

### Usage
- `func f(x *int) { *x = 10 }` - Function with pointer parameter
- `f(&x)` - Pass address
- `func f() *int { x := 10; return &x }` - Return pointer (Go manages memory)

### Pointer to Struct
- `p := &Person{Name: "Alice"}` - Pointer to struct
- `p.Name` - Access field (auto-dereference)
- `(*p).Name` - Explicit dereference

---

## Control Flow

### If/Else
- `if condition { ... }` - If statement
- `if condition { ... } else { ... }` - If-else
- `if x := f(); x < 10 { ... }` - If with initialization
- `if x, err := f(); err != nil { ... }` - Common pattern

### Switch
- `switch x { case 1: ... case 2: ... default: ... }` - Switch statement
- `switch { case x < 10: ... }` - Switch without expression (if-else chain)
- `switch x := f(); x { case 1: ... }` - Switch with initialization
- `switch x.(type) { case int: ... case string: ... }` - Type switch
- `fallthrough` - Continue to next case

### For Loops
- `for i := 0; i < 10; i++ { ... }` - Traditional for loop
- `for condition { ... }` - While loop
- `for { ... }` - Infinite loop
- `for i, v := range slice { ... }` - Range over slice
- `for k, v := range map { ... }` - Range over map
- `for i, r := range "hello" { ... }` - Range over string (runes)
- `for _, v := range slice { ... }` - Ignore index
- `break` - Exit loop
- `continue` - Skip iteration
- `break label` - Break outer loop

---

## Functions

### Definition
- `func add(a int, b int) int { return a + b }` - Basic function
- `func add(a, b int) int { return a + b }` - Same type parameters
- `func swap(a, b int) (int, int) { return b, a }` - Multiple return values
- `func f() (x int, y string) { x = 1; y = "a"; return }` - Named returns
- `func f() error { return nil }` - Return error

### Variadic Functions
- `func sum(nums ...int) int { ... }` - Variadic parameter
- `sum(1, 2, 3)` - Call with multiple args
- `sum(nums...)` - Spread slice

### Function Values
- `f := func(x int) int { return x * 2 }` - Anonymous function
- `f(5)` - Call function value
- `func apply(fn func(int) int, x int) int { return fn(x) }` - Function parameter

### Closures
- `func counter() func() int { x := 0; return func() int { x++; return x } }` - Closure
- `c := counter(); c()` - Capture variable

### Defer
- `defer f()` - Defer function call (executes at end of function)
- `defer f(x)` - Arguments evaluated immediately
- `defer func() { ... }()` - Defer anonymous function
- Multiple defers execute in LIFO order

---

## Error Handling

### Error Type
- `type error interface { Error() string }` - Error interface
- `errors.New("message")` - Create error
- `fmt.Errorf("format: %v", err)` - Format error
- `var ErrNotFound = errors.New("not found")` - Sentinel error

### Error Handling Pattern
- `result, err := f()` - Function returns error
- `if err != nil { return err }` - Check and return error
- `if err != nil { log.Fatal(err) }` - Log and exit
- `if err != nil { return fmt.Errorf("context: %w", err) }` - Wrap error

### Custom Errors
- `type MyError struct { Code int; Msg string }`
- `func (e *MyError) Error() string { return e.Msg }`
- `errors.Is(err, ErrNotFound)` - Check error (Go 1.13+)
- `errors.As(err, &myErr)` - Assert error type (Go 1.13+)

### Panic & Recover
- `panic("message")` - Trigger panic
- `defer func() { if r := recover(); r != nil { ... } }()` - Recover from panic
- Use panic for unrecoverable errors, error for expected failures

---

## Concurrency

### Goroutines
- `go f()` - Start goroutine
- `go func() { ... }()` - Anonymous goroutine
- `runtime.GOMAXPROCS(4)` - Set max CPUs

### Channels
- `ch := make(chan int)` - Unbuffered channel
- `ch := make(chan int, 10)` - Buffered channel (capacity 10)
- `ch <- value` - Send to channel
- `value := <-ch` - Receive from channel
- `value, ok := <-ch` - Receive with closed check
- `close(ch)` - Close channel
- `for v := range ch { ... }` - Range over channel

### Select
- `select { case <-ch1: ... case <-ch2: ... default: ... }` - Select from channels
- `select { case ch <- value: ... case <-done: return }` - Send or receive
- Blocks until one case ready (or default if present)

### Sync Primitives
- `sync.Mutex` - Mutual exclusion lock
- `mu.Lock(); defer mu.Unlock()` - Lock pattern
- `sync.RWMutex` - Read-write lock
- `sync.WaitGroup` - Wait for goroutines
- `wg.Add(1); go f(); wg.Wait()` - WaitGroup pattern
- `sync.Once` - Execute once
- `once.Do(func() { ... })` - Once pattern

### Context (Go 1.7+)
- `ctx := context.Background()` - Root context
- `ctx, cancel := context.WithCancel(parent)` - Cancelable context
- `ctx, cancel := context.WithTimeout(parent, time.Second)` - Timeout context
- `ctx, cancel := context.WithDeadline(parent, deadline)` - Deadline context
- `select { case <-ctx.Done(): return ctx.Err() }` - Check cancellation

---

## File I/O

### Reading Files
- `data, err := os.ReadFile("file.txt")` - Read entire file (Go 1.16+)
- `file, err := os.Open("file.txt")` - Open file
- `defer file.Close()` - Close file
- `data := make([]byte, 100); n, err := file.Read(data)` - Read bytes
- `scanner := bufio.NewScanner(file); for scanner.Scan() { ... }` - Read lines

### Writing Files
- `err := os.WriteFile("file.txt", data, 0644)` - Write entire file (Go 1.16+)
- `file, err := os.Create("file.txt")` - Create file
- `file.WriteString("hello\n")` - Write string
- `file.Write(data)` - Write bytes
- `file, err := os.OpenFile("file.txt", os.O_APPEND|os.O_WRONLY, 0644)` - Append mode

### JSON
- `json.Marshal(v)` - Encode to JSON
- `json.Unmarshal(data, &v)` - Decode from JSON
- `json.NewEncoder(w).Encode(v)` - Stream encode
- `json.NewDecoder(r).Decode(&v)` - Stream decode
- `` `json:"field_name"` `` - Struct tag

### YAML
- `yaml.Marshal(v)` - Encode to YAML
- `yaml.Unmarshal(data, &v)` - Decode from YAML

---

## Packages & Modules

### Package Declaration
- `package main` - Main package (executable)
- `package mypkg` - Library package

### Imports
- `import "fmt"` - Import package
- `import ("fmt"; "os")` - Multiple imports
- `import f "fmt"` - Import with alias
- `import _ "package"` - Side-effect import
- `import . "fmt"` - Dot import (use without package name)

### Modules (Go 1.11+)
- `go mod init github.com/user/repo` - Initialize module
- `go.mod` - Module definition file
- `go get package@version` - Add dependency
- `go get -u package` - Update dependency
- `go mod tidy` - Clean dependencies
- `go mod vendor` - Vendor dependencies

### Common Standard Packages
- `fmt` - Formatting and I/O
- `os` - Operating system interface
- `io` - I/O primitives
- `strings` - String manipulation
- `strconv` - String conversions
- `time` - Time operations
- `encoding/json` - JSON encoding/decoding
- `net/http` - HTTP client and server
- `context` - Context for cancellation
- `sync` - Synchronization primitives
- `log` - Logging
- `testing` - Testing support

---

## Common Built-in Functions

### Allocation
- `new(T)` - Allocate zeroed value, return *T
- `make(T, ...)` - Allocate and initialize slice, map, or channel

### Length & Capacity
- `len(s)` - Length of string, array, slice, map, channel
- `cap(s)` - Capacity of array, slice, channel

### Type Conversion
- `int(x)`, `float64(x)`, `string(x)` - Type conversion
- `[]byte(s)` - String to byte slice
- `string(b)` - Byte slice to string

### Copy & Append
- `copy(dst, src)` - Copy slice
- `append(slice, elements...)` - Append to slice

### Delete
- `delete(map, key)` - Delete map key

### Panic & Recover
- `panic(v)` - Trigger panic
- `recover()` - Recover from panic (only in defer)

### Complex Numbers
- `complex(real, imag)` - Create complex number
- `real(c)`, `imag(c)` - Get real/imaginary parts

### Channel Operations
- `close(ch)` - Close channel

---

## Best Practices

- **Error handling**: Always check errors, don't ignore them
- **Context**: Pass context.Context for cancellation and timeouts
- **Interfaces**: Define small, focused interfaces
- **Pointers**: Use pointers for large structs or when mutating
- **Goroutines**: Always know when goroutines will exit
- **Channels**: Close channels when done sending
- **Defer**: Use defer for cleanup (files, locks, etc.)
- **Naming**: Use camelCase for exported, lowercase for unexported
- **Testing**: Write tests in `*_test.go` files
- **Modules**: Use Go modules for dependency management
- **Formatting**: Run `go fmt` before committing
- **Linting**: Use `golangci-lint` for static analysis
- **Documentation**: Write godoc comments for exported functions

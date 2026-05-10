# C Programming Reference

**Focus**: Systems programming, low-level operations, memory management, performance-critical code, embedded systems, and system integration.

## Table of Contents

- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Pointers & Memory](#pointers--memory)
- [Arrays & Strings](#arrays--strings)
- [Structures & Unions](#structures--unions)
- [File I/O](#file-io)
- [Error Handling](#error-handling)
- [Memory Management](#memory-management)
- [Common Standard Library Functions](#common-standard-library-functions)
- [Preprocessor Directives](#preprocessor-directives)
- [Common Patterns](#common-patterns)

---

## Built-In Syntax

### Type modifiers
- `const` - read-only variable
- `volatile` - value may change unexpectedly (hardware registers, signals)
- `static` - internal linkage (file scope) or persistent storage
- `extern` - external linkage (defined elsewhere)
- `register` - hint to store in register (rarely used, compiler optimizes)
- `auto` - automatic storage (default, rarely explicit)

### Storage classes
- `static` - static storage duration (persists for program lifetime)
- `extern` - external linkage
- `auto` - automatic storage (default)
- `register` - register storage hint

### Control flow
- `if` - conditional branch
- `else` - default branch
- `switch` - multi-way branch
- `case` - switch case label
- `default` - switch default case
- `for` - iteration loop
- `while` - loop while condition true
- `do` - do-while loop
- `break` - exit loop/switch
- `continue` - skip to next iteration
- `goto` - jump to label (use sparingly)
- `return` - return from function

### Other keywords
- `sizeof` - size of type/variable in bytes
- `typedef` - create type alias
- `struct` - define structure
- `union` - define union
- `enum` - define enumeration
- `void` - no type / no return value

---

## Variables & Basic Types

- **Integers**: `int`, `short`, `long`, `long long`; `unsigned` variants
- **Floating**: `float` (32-bit), `double` (64-bit), `long double`
- **Character**: `char`, `signed char`, `unsigned char`
- **Boolean** (C99+): `#include <stdbool.h>`, `bool`, `true`, `false`
- **Void**: `void *ptr` (generic pointer)

**Type Sizes** (64-bit): `char` 1, `short` 2, `int` 4, `long` 8, `long long` 8, `float` 4, `double` 8, `pointer` 8

**Qualifiers**: `const` (read-only), `volatile` (may change externally), `restrict` (C99, optimization hint)

**Memory Model**: C has no automatic memory management - variables on stack (automatic), heap requires manual `malloc()`/`free()`. No garbage collection, no bounds checking. Stack variables destroyed when scope ends; heap memory persists until `free()`.

---

## Operators

- **Arithmetic**: `+ - * / % ++ --`
- **Comparison**: `== != < > <= >=`
- **Logical**: `&& || !`
- **Bitwise**: `& | ^ ~ << >>`
- **Assignment**: `= += -= *= /= %= &= |= ^= <<= >>=`
- **Address/Dereference**: `&var` (address), `*ptr` (dereference)
- **Member access**: `.` (direct), `->` (via pointer)
- **Array/pointer**: `[]` (subscript/arithmetic)

---

## Control Flow

- **If/Else**: `if (cond) { } else if (cond) { } else { }`
- **Ternary**: `x = (a > b) ? a : b`
- **Switch**: `switch (val) { case 1: break; default: break; }` (fall-through without break)
- **For**: `for (int i = 0; i < 10; i++) { }`; `for (;;) { }` (infinite)
- **While**: `while (cond) { }`; `do { } while (cond);` (executes at least once)
- **Control**: `break` (exit loop/switch), `continue` (next iteration), `goto` (use sparingly)

---

## Functions

- **Definition**: `int add(int a, int b) { return a + b; }`
- **Declaration**: `int multiply(int x, int y);` (prototype)
- **No params**: `void func(void);` (explicit void)
- **Return pointer**: `int* get_array(void);`
- **Array param**: `void process(int arr[], int size);` (decays to `int *arr`)
- **Variadic** (`<stdarg.h>`): `int sum(int count, ...)` with `va_list`, `va_start()`, `va_arg()`, `va_end()`
- **Function pointer**: `int (*op)(int, int); op = add; result = op(3, 4);`

---

## Pointers & Memory

**Why pointers?** C passes arguments by value (copied). Pointers allow: passing large data efficiently, modifying caller's variables, dynamic memory allocation, implementing data structures.

- **Declaration**: `int *ptr;` (pointer to int), `int **pptr;` (pointer to pointer)
- **Address**: `int *p = &x;` (get address of x) - `&` gets memory address
- **Dereference**: `int val = *p;` (get value), `*p = 20;` (modify) - `*` accesses value at address
- **Null**: `int *ptr = NULL;` (use NULL, not 0); check `if (ptr != NULL)` before dereference - **Dereferencing NULL crashes**
- **Arithmetic**: `p++` (next element, advances by `sizeof(int)`), `*(p + 2) = 100;` (offset), `p += 5;` (advance)
- **Comparison**: `if (p1 < p2)` (compare addresses, not values)
- **Void pointer**: `void *ptr;` (generic, can point to any type), cast: `int *p = (int*)ptr;` - Must cast before use
- **Const pointers**: `const int *p` (pointer to const - can't modify value), `int *const p` (const pointer - can't change address)

**Common pitfalls**: Dereferencing NULL/uninitialized pointer (crash), dereferencing freed pointer (undefined behavior), pointer arithmetic out of bounds (undefined behavior)

**Best Practices**: Initialize to NULL, check NULL before dereference, don't dereference freed pointers, be aware of pointer arithmetic bounds

---

## Arrays & Strings

### Arrays

- **Declaration**: `int arr[10];` (fixed size), `int arr[] = {1, 2, 3};` (initialized), `int arr[5] = {0};` (zeros)
- **Access**: `arr[0] = 100;`, `int val = arr[5];` - **No bounds checking** (accessing `arr[10]` on size-10 array is undefined behavior)
- **Size**: `sizeof(arr) / sizeof(arr[0])` (number of elements) - Only works in same scope as declaration
- **Multi-dimensional**: `int matrix[3][4];`, `matrix[0][0] = 1;`
- **Function param**: `void process(int arr[], int size);` (decays to `int *arr`) - **Arrays decay to pointers when passed to functions** - size information lost, must pass size separately
- **Array vs pointer**: `int arr[10]` (array, `sizeof(arr)` = 40), `int *ptr` (pointer, `sizeof(ptr)` = 8) - Arrays are not pointers, but decay to pointers in most contexts

### Strings (`<string.h>`)

- **Literals**: `char *str = "Hello";` (immutable, read-only)
- **Arrays**: `char buffer[100];`, `char name[] = "Alice";` (mutable)
- **Functions**: `strlen()`, `strcpy()` (unsafe), `strncpy()` (safer), `strcat()` (unsafe), `strncat()` (safer)
- **Comparison**: `strcmp()` (0 if equal), `strncmp()` (first n chars)
- **Search**: `strstr()` (substring), `strchr()` (character)
- **Safe ops** (C11): `strcpy_s()`, `strcat_s()` (bounds-checked)

**Best Practices**: Always null-terminate (`\0`), use `strncpy`/`strncat`, check buffer sizes, prefer C11 safe functions

---

## Structures & Unions

### Structures

- **Definition**: `struct Point { int x; int y; };`
- **Variable**: `struct Point p1; p1.x = 10;`
- **Initialization**: `struct Point p2 = {10, 20};` (positional), `{.x = 10, .y = 20}` (C99 designated)
- **Pointer**: `struct Point *ptr = &p1; ptr->x = 30;` (or `(*ptr).y = 40;`)
- **Typedef**: `typedef struct { int x; int y; } Point;` (no `struct` keyword needed)
- **Nested**: `struct Rect { struct Point top_left; struct Point bottom_right; };`
- **Function pointers**: `struct Calc { int (*add)(int, int); };`

### Unions

- **Definition**: `union Data { int i; float f; char str[20]; };` (shares memory)
- **Usage**: `union Data d; d.i = 10; d.f = 3.14;` (overwrites previous)
- **Variant type**: Combine with enum to track active member

### Enumerations

- **Definition**: `enum Status { PENDING, ACTIVE, INACTIVE };`
- **With values**: `enum ErrorCode { SUCCESS = 0, ERROR_INVALID = 1 };`
- **Usage**: `enum Status s = ACTIVE;`

---

## File I/O (`<stdio.h>`)

- **Open**: `FILE *fp = fopen("file.txt", "r");` (modes: `"r"`, `"w"`, `"a"`, `"r+"`; add `"b"` for binary)
- **Check**: `if (fp == NULL) { perror("fopen"); return 1; }`
- **Read**: `fgets(buffer, size, fp)` (line-by-line), `fscanf(fp, "%d", &num)` (formatted)
- **Write**: `fprintf(fp, "Value: %d\n", num);`, `fputs("Hello\n", fp);`, `fputc('A', fp);`
- **Binary**: `fread(ptr, size, count, fp);`, `fwrite(ptr, size, count, fp);`
- **Close**: `fclose(fp);`
- **Error check**: `ferror(fp)`, `feof(fp)`

**Best Practices**: Check `fopen()` return, always `fclose()`, use `fgets()` not `gets()`, check return values, use `"b"` for binary

---

## Error Handling (`<errno.h>`, `<string.h>`)

- **errno**: Global error number; check after system calls return -1
- **Error messages**: `strerror(errno)`, `perror("function_name")`
- **Return codes**: Return 0 for success, negative for errors
- **Pattern**: `if (result == NULL) return -1;` (validate inputs), `return 0;` (success)

**Best Practices**: Check return values, use `errno` for system errors, return meaningful codes, use `perror()`/`strerror()` for messages

---

## Memory Management (`<stdlib.h>`)

**Manual memory management**: C requires explicit allocation/deallocation. No garbage collection - memory leaks if not freed. Heap memory persists until `free()`.

- **Allocate**: `int *arr = malloc(10 * sizeof(int));` (check for NULL) - Returns NULL on failure, **always check**
- **Zero-init**: `int *arr = calloc(10, sizeof(int));` (allocate and zero) - All bytes set to 0
- **Reallocate**: `arr = realloc(arr, 20 * sizeof(int));` (may move memory, check NULL) - **May return different address**, old pointer invalid if moved
- **Free**: `free(arr); arr = NULL;` (prevent dangling pointer) - **Freeing NULL is safe** (no-op), freeing twice is undefined behavior

**Common pitfalls**: Memory leaks (forgot to free), use-after-free (dereference freed pointer), double-free (free same pointer twice), buffer overflow (write past allocated memory)

**Best Practices**: Always check NULL return, always free, set to NULL after free, don't free twice, don't dereference freed pointers, use `calloc()` for zero-init, match every `malloc()` with `free()`

---

## Common Standard Library Functions

### stdio.h
- **I/O**: `printf()`, `scanf()` (unsafe, prefer `fgets` + `sscanf`), `fprintf(stderr, ...)`
- **String format**: `sprintf()` (unsafe), `snprintf()` (safe, C99)
- **Char I/O**: `getchar()`, `putchar()`

### stdlib.h
- **Memory**: `malloc()`, `calloc()`, `realloc()`, `free()`
- **Conversion**: `atoi()`, `atol()`, `atof()` (unsafe), `strtol()`, `strtod()` (with error checking)
- **Random**: `srand(time(NULL))`, `rand()` (returns [0, RAND_MAX])
- **Exit**: `exit(0)` (success), `exit(1)` (error)

### string.h
- **Strings**: `strlen()`, `strcpy()`/`strncpy()`, `strcat()`/`strncat()`, `strcmp()`/`strncmp()`, `strstr()`, `strchr()`
- **Memory**: `memcpy()` (copy), `memmove()` (handles overlap), `memset()` (set), `memcmp()` (compare)

### ctype.h
- **Character checks**: `isalpha()`, `isdigit()`, `isalnum()`, `isspace()`, `isupper()`, `islower()`
- **Conversion**: `toupper()`, `tolower()`

---

## Preprocessor Directives

- **Include**: `#include <stdio.h>` (system), `#include "myheader.h"` (local)
- **Define**: `#define MAX_SIZE 100`, `#define MIN(a, b) ((a) < (b) ? (a) : (b))` (macro with params, parenthesize)
- **Conditional**: `#ifdef DEBUG`, `#ifndef`, `#if defined(DEBUG) && defined(VERBOSE)`, `#endif`
- **Predefined**: `__LINE__`, `__FILE__`, `__DATE__`, `__TIME__`, `__func__` (C99)
- **Error**: `#error "message"` (stop compilation)
- **Pragma**: `#pragma once` (include guard, non-standard but common)

---

## Common Patterns

### Safe String Copy
- Validate inputs (NULL checks, size > 0)
- Copy up to `dest_size - 1` characters
- Always null-terminate
- Return 0 if fit, -1 if truncated

### Dynamic Array
- Structure: `{ int *data; size_t size; size_t capacity; }`
- Create: Allocate struct, then data array; check both for NULL
- Destroy: Free data, then struct; set to NULL

### Error Handling
- Enum for error codes: `enum { SUCCESS = 0, ERROR_NULL = -1, ERROR_INVALID = -2 };`
- Validate inputs first, return early on error
- Return 0 for success, negative for errors

### Resource Management
- Pair allocation with deallocation: `fopen()` → `fclose()`, `malloc()` → `free()`
- Check for NULL after allocation
- Always cleanup in error paths

---

---

## Undefined Behavior

**Critical concept**: C allows operations that have undefined behavior - program may crash, produce wrong results, or appear to work. Common causes:

- **Dereferencing NULL/uninitialized pointer**: Crash or corruption
- **Buffer overflow**: Writing past array/allocated memory bounds
- **Use after free**: Accessing freed memory
- **Double free**: Freeing same pointer twice
- **Uninitialized variables**: Reading before assignment (may contain garbage)
- **Signed integer overflow**: Arithmetic that exceeds type limits
- **Accessing out-of-bounds array**: `arr[10]` on size-10 array

**No runtime checks**: C trusts the programmer - no automatic bounds checking, no null pointer checks, no type safety at runtime. Compiler may not warn about all issues.

---

> **Note**: C is a low-level language with manual memory management and no runtime safety checks. Always validate inputs, check return values, manage memory carefully, and be aware of undefined behavior. For higher-level abstractions with automatic memory management, consider C++ or other languages.


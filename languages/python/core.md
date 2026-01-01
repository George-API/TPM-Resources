# Python Syntax Reference

**Focus**: Tight syntax reference for Python 3 - keywords, operators, data types, control flow, functions, classes, exceptions, I/O.

## Table of Contents

- [Environment Setup](#environment-setup)
- [Type System & Memory](#type-system--memory)
- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Strings](#strings)
- [Lists (mutable, ordered)](#lists-mutable-ordered)
- [Tuples (immutable, ordered)](#tuples-immutable-ordered)
- [Sets (mutable, unordered, unique elements)](#sets-mutable-unordered-unique-elements)
- [Dictionaries (mutable, key-value pairs)](#dictionaries-mutable-key-value-pairs)
- [Control Flow](#control-flow)
- [Match Statement (Python 3.10+)](#match-statement-python-310)
- [Functions](#functions)
- [Type Hints & Annotations](#type-hints--annotations)
- [Classes & OOP](#classes--oop)
- [Exception Handling](#exception-handling)
- [File I/O](#file-io)
- [Modules & Packages](#modules--packages)
- [Common Built-in Functions](#common-built-in-functions)

---
## Environment Setup
- Create venv: `python -m venv`
- Activate venv: `Scripts\activate`
- Add packages to requirements.txt
- `pip install -r requirements.txt`

---

## Built-In Syntax

### Values
- `True` - Boolean true value
- `False` - Boolean false value
- `None` - Null / no value

### Logical / comparison
- same object)
- `is not` - Negative identity comparison
- `in` - Membership test`and` - Logical AND
- `or` - Logical OR
- `not` - Logical NOT
- `is` - Identity comparison (
- `not in` - Negative membership test

### Conditionals
- `if` - Conditional branch
- `elif` - Else-if branch
- `else` - Default branch
- `match` - Pattern matching (Python 3.10+)

### Loops
- `for` - Iterate over iterable
- `while` - Loop while condition true
- `break` - Exit loop immediately
- `continue` - Skip to next iteration
- `pass` - No-op placeholder

### Functions / callables
- `def` - Define function
- `return` - Return value from function
- `lambda` - Anonymous inline function
- `yield` - Produce value from generator

### Classes
- `class` - Define class

### Exceptions / debugging
- `try` - Start exception handling
- `except` - Catch exception
- `else` - Run if no exception
- `finally` - Always run cleanup
- `raise` - Raise exception
- `assert` - Debug-time condition check

### Imports
- `import` - Import module
- `from` - Import specific names
- `as` - Alias name

### Context management
- `with` - Context manager (auto cleanup)

### Scope / memory
- `global` - Declare global variable
- `nonlocal` - Declare enclosing-scope variable
- `del` - Delete reference (removes binding, object deleted by GC if no other references)

### Type annotations (Python 3.5+)
- `:` - Type annotation (variable, parameter)
- `->` - Return type annotation
- `type` - Type hints for static analysis


## Variables & Basic Types

### Variable Assignment
- `x = 10` - int
- `y = 3.14` - float
- `name = "Alice"` - str
- `is_valid = True` - bool
- `nothing = None` - NoneType

### Multiple Assignment
- `a, b, c = 1, 2, 3` - unpack multiple values
- `x = y = z = 0` - assign same value to multiple variables

### Type Conversion
- `int("42")` - convert to int
- `float("3.14")` - convert to float
- `str(123)` - convert to string
- `bool(1)` - convert to boolean

**Truthy/Falsy Values**
- Falsy: `False`, `None`, `0`, `0.0`, `""`, `[]`, `{}`, `()`
- Everything else is truthy

## Operators

### Arithmetic
- `+` - add
- `-` - subtract
- `*` - multiply
- `/` - divide
- `//` - floor division
- `%` - modulo
- `**` - power

### Comparison
- `==` - equality
- `!=` - inequality
- `<`, `>`, `<=`, `>=` - relational operators

### Logical
- `and` - logical AND
- `or` - logical OR
- `not` - logical NOT

### Membership & Identity
- `in`, `not in` - membership test
- `is`, `is not` - identity test (same object)

### Augmented Assignment
- `+=`, `-=`, `*=`, `/=`, `//=`, `%=`, `**=`

## Strings

### String Creation
- `s = "hello"` - double quotes
- `s = 'hello'` - single quotes
- `s = """multiline\nstring"""` - triple quotes (multiline)

### f-strings (Python 3.6+)
- `f"Name: {name}, Age: {age}"` - formatted string with variables
- `f"{age * 2}"` - expressions in f-strings

### Common Methods
- `s.upper()` - convert to uppercase
- `s.lower()` - convert to lowercase
- `s.strip()` - remove leading/trailing whitespace
- `s.split(",")` - split into list
- `",".join(["a","b"])` - join list into string
- `s.replace("h","j")` - replace substring
- `s.startswith("he")` - check if starts with
- `s.endswith("lo")` - check if ends with
- `s.find("ll")` - find index (returns -1 if not found)

### Indexing & Slicing
- `s[0]` - get character at index
- `s[1:4]` - slice substring
- `len(s)` - get length

## Lists (mutable, ordered)

### Creation
- `lst = [1, 2, 3, 4]` - create list
- `lst = []` - empty list

### Indexing & Slicing
- `lst[0]` - first element
- `lst[-1]` - last element
- `lst[1:3]` - slice from index 1 to 3 (exclusive)
- `lst[:2]` - first two elements
- `lst[2:]` - from index 2 to end
- `lst[::2]` - every 2nd element

### Methods
- `lst.append(5)` - add to end
- `lst.extend([6,7])` - add multiple elements
- `lst.insert(0, 0)` - insert at index
- `lst.remove(3)` - remove first occurrence
- `lst.pop()` - remove & return last element
- `lst.pop(0)` - remove & return element at index
- `lst.sort()` - sort in place
- `lst.reverse()` - reverse in place
- `lst.index(2)` - find index of value
- `lst.count(2)` - count occurrences
- `lst.clear()` - remove all elements

### Built-in Functions
- `len(lst)` - length
- `sorted(lst)` - return sorted copy
- `reversed(lst)` - return reversed iterator
- `min(lst)`, `max(lst)` - min/max values
- `sum(lst)` - sum of elements

### List Comprehension
- `[x*2 for x in range(5)]` - transform elements
- `[x for x in range(10) if x % 2 == 0]` - filter elements

## Tuples (immutable, ordered)

### Creation
- `tup = (1, 2, 3)` - create tuple
- `tup = 1, 2, 3` - parentheses optional
- `tup = (1,)` - single element tuple (note comma)

### Indexing & Slicing
- `tup[0]` - get element at index
- `tup[1:]` - slice tuple

### Unpacking
- `x, y, z = tup` - unpack into variables
- `x, *rest = tup` - unpack with remainder

## Sets (mutable, unordered, unique elements)

### Creation
- `s = {1, 2, 3}` - create set
- `s = set()` - empty set (not {})

### Methods
- `s.add(4)` - add element
- `s.remove(2)` - remove (raises error if not found)
- `s.discard(2)` - remove (no error if not found)
- `s.clear()` - remove all

### Set Operations
- `a | b` - union
- `a & b` - intersection
- `a - b` - difference
- `a ^ b` - symmetric difference

### Set Comprehension
- `{x*2 for x in range(5)}` - create set from comprehension

## Dictionaries (mutable, key-value pairs)

### Creation
- `d = {"name": "Alice", "age": 30}` - create dict
- `d = {}` - empty dict
- `d = dict(name="Alice", age=30)` - alternative syntax

### Access
- `d["name"]` - get value by key (raises KeyError if missing)
- `d.get("name")` - get value (returns None if missing)
- `d.get("city", "N/A")` - get with default value

### Modify
- `d["age"] = 31` - update value
- `d["city"] = "NYC"` - add new key

### Methods
- `d.keys()` - get all keys
- `d.values()` - get all values
- `d.items()` - get all key-value pairs
- `d.pop("age")` - remove & return value
- `d.update({"x": 1})` - merge another dict
- `d.clear()` - remove all

### Membership
- `"name" in d` - check if key exists

### Dict Comprehension
- `{x: x*2 for x in range(5)}` - create dict from comprehension

## Control Flow

### If/Elif/Else
- `if condition:` - conditional branch
- `elif condition:` - else-if branch
- `else:` - default branch
- `x = value_if_true if condition else value_if_false` - ternary operator
- `if 0 < x < 10:` - chained comparisons

### For Loops
- `for item in iterable:` - iterate over iterable
- `for i in range(5):` - range(5) = 0,1,2,3,4
- `for i in range(2, 5):` - range(2,5) = 2,3,4
- `for i in range(0, 10, 2):` - range with step = 0,2,4,6,8
- `for i, val in enumerate(lst):` - iterate with index and value
- `for x, y in zip(list1, list2):` - parallel iteration
- `break` - exit loop immediately
- `continue` - skip to next iteration

### While Loops
- `while condition:` - loop while condition true
- `while condition: ... else:` - else runs if loop completes without break

---

## Match Statement (Python 3.10+)
- `match value: case 1:` - match literal value
- `case 2 | 3:` - match multiple values
- `case int(x) if x > 10:` - match type with guard clause
- `case [x, y]:` - match sequence pattern
- `case {"name": str(name), "age": int(age)}:` - match dict pattern
- `case Point(x=0, y=0):` - match class pattern
- `case _:` - wildcard (default case)

## Functions
- `def function_name(param1, param2):` - define function
- `def func(param="default"):` - default arguments (mutable defaults dangerous - use None)
- `def func(*args):` - variable positional args (tuple)
- `def func(**kwargs):` - variable keyword args (dict)
- `def func(a, b, *, kw_only):` - keyword-only args (Python 3+)
- `def func(pos_only, /, normal, *, kw_only):` - positional-only args (Python 3.8+)
- `lambda x: x * 2` - anonymous function
- `map(func, iterable)`, `filter(func, iterable)`, `reduce(func, iterable)` - functional tools

## Type Hints & Annotations (Python 3.5+)
- `def func(name: str) -> str:` - basic type hints
- `def func(value: Union[int, str]) -> Optional[str]:` - Union, Optional types
- `def func(items: list[int]) -> dict[str, int]:` - generic types (Python 3.9+)
- `UserId: TypeAlias = int` - type aliases
- `class UserDict(TypedDict): name: str; age: int` - TypedDict (Python 3.8+)
- `T = TypeVar('T'); def first(items: list[T]) -> T:` - type variables (generics)
- **Common types**: `int`, `str`, `float`, `bool`, `list[T]`, `dict[K, V]`, `tuple[T, ...]`, `set[T]`, `Optional[T]`, `Union[A, B]`, `Any`
- **Type checking**: Use `mypy` or `pyright` - types are hints, not enforced at runtime

---

## Classes & OOP

### Class Definition
- `class ClassName:` - define class
- `class_var = 0` - class variable (shared by all instances)
- `def __init__(self, param1: str, param2: int):` - constructor
- `self.param1 = param1` - instance variable

### Methods
- `def method(self) -> str:` - instance method
- `@classmethod def class_method(cls) -> int:` - class method (receives class, not instance)
- `@staticmethod def static_method() -> str:` - static method (no self or cls)
- `@property def computed(self) -> int:` - property (accessed like attribute)

### Special Methods
- `__init__()` - constructor
- `__str__()` - string representation (user-friendly)
- `__repr__()` - string representation (developer-friendly, should be valid Python)
- `__eq__()`, `__lt__()`, `__len__()`, `__getitem__()`, `__setitem__()`, `__contains__()`, `__call__()` - other special methods

### Instantiation & Usage
- `obj = ClassName("value1", 42)` - create instance
- `obj.method()` - call instance method
- `ClassName.class_method()` - call class method
- `obj.computed` - access property

### Inheritance
- `class Child(Parent):` - single inheritance
- `class Child(Parent1, Parent2):` - multiple inheritance
- `super().__init__(param1, param2)` - call parent constructor
- `super().method()` - call parent method

### Dataclasses (Python 3.7+)
- `from dataclasses import dataclass` - import decorator
- `@dataclass class Point: x: int; y: int; z: int = 0` - auto-generates `__init__`, `__repr__`, `__eq__`
- Use for data containers

### Slots (Memory Optimization)
- `__slots__ = ('x', 'y')` - prevents `__dict__` creation, saves memory for many instances

## Exception Handling

### Try/Except
- `try: ... except Exception as e:` - catch exception
- `except ZeroDivisionError as e:` - catch specific exception
- `except (TypeError, ValueError) as e:` - catch multiple exceptions
- `else:` - executes if no exception raised
- `finally:` - always executes (cleanup code)

### Raising Exceptions
- `raise ValueError("Invalid value")` - raise exception
- `raise Exception("Custom error message")` - raise generic exception
- `raise RuntimeError("Failed") from e` - exception chaining (Python 3+)

### Exception Groups (Python 3.11+)
- `except* ValueError as eg:` - catch multiple exceptions
- `for exc in eg.exceptions:` - iterate over exceptions

### Common Exceptions
- `ValueError`, `TypeError`, `KeyError`, `IndexError`, `AttributeError`
- `FileNotFoundError`, `ZeroDivisionError`, `ImportError`, `StopIteration`

**Exception hierarchy**: `BaseException` → `Exception` → `RuntimeError`, `ValueError`, etc. - Catch `Exception`, not `BaseException`

## File I/O

### Reading Files
- `with open("file.txt", "r") as f: content = f.read()` - read entire file
- `with open("file.txt", "r") as f: lines = f.readlines()` - read as list of lines
- `with open("file.txt", "r") as f: for line in f:` - iterate line by line

### Writing Files
- `with open("file.txt", "w") as f: f.write("Hello\n")` - write string
- `with open("file.txt", "w") as f: f.writelines(["line1\n", "line2\n"])` - write multiple lines
- `with open("file.txt", "a") as f: f.write("Appended\n")` - append to file

### File Modes
- `"r"` - read, `"w"` - write (overwrites), `"a"` - append, `"r+"` - read/write
- `"rb"`, `"wb"` - binary mode

## Modules & Packages

### Import Entire Module
- `import math` - import module
- `math.sqrt(16)` - use module function

### Import with Alias
- `import numpy as np` - import with alias
- `np.array([1, 2, 3])` - use aliased module

### Import Specific Items
- `from math import sqrt, pi` - import specific functions/constants
- `sqrt(16)` - use directly

### Import All (not recommended)
- `from math import *` - import all (pollutes namespace)

### Import from Package
- `from package.module import function` - import from submodule

### Module Inspection
- `dir(math)` - list all attributes
- `help(math.sqrt)` - show documentation

## Common Built-in Functions

### Type & Conversion
- `type(x)` - get type
- `isinstance(x, int)` - check type
- `int()`, `float()`, `str()`, `bool()`, `list()`, `tuple()`, `set()`, `dict()` - type constructors

### Iteration
- `range(start, stop, step)` - create range
- `enumerate(iterable, start=0)` - get index and value
- `zip(iter1, iter2, ...)` - combine iterables
- `reversed(sequence)` - reverse sequence
- `sorted(iterable, key=None, reverse=False)` - return sorted list

### Aggregation
- `len(obj)` - get length
- `sum(iterable, start=0)` - sum elements
- `min(iterable)`, `max(iterable)` - min/max values
- `all(iterable)` - True if all elements truthy
- `any(iterable)` - True if any element truthy

### I/O
- `print(*objects, sep=' ', end='\n')` - print to stdout
- `input(prompt)` - read string from stdin

### Other
- `abs(x)` - absolute value
- `round(x, n)` - round to n decimals
- `pow(x, y)` - x to power y
- `divmod(x, y)` - return (x//y, x%y)  
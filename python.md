# Python Quick Reference Guide

## Built-In Syntax

### Values
True            # Boolean true value
False           # Boolean false value
None            # Null / no value

### Logical / comparison
and             # Logical AND
or              # Logical OR
not             # Logical NOT
is              # Identity comparison (same object)
is not          # Negative identity comparison
in              # Membership test
not in          # Negative membership test

### Conditionals
if              # Conditional branch
elif            # Else-if branch
else            # Default branch


### Loops
for             # Iterate over iterable
while           # Loop while condition true
break           # Exit loop immediately
continue        # Skip to next iteration
pass            # No-op placeholder


### Functions / callables
def             # Define function
return          # Return value from function
lambda          # Anonymous inline function
yield           # Produce value from generator


### Classes
class           # Define class


### Exceptions / debugging
try             # Start exception handling
except          # Catch exception
else            # Run if no exception
finally         # Always run cleanup
raise           # Raise exception
assert          # Debug-time condition check


### Imports
import          # Import module
from            # Import specific names
as              # Alias name


### Context management
with            # Context manager (auto cleanup)


### Scope / memory
global          # Declare global variable
nonlocal        # Declare enclosing-scope variable
del             # Delete reference


## Variables & Basic Types

```python
# Variable assignment (dynamically typed, no declaration needed)
x = 10              # int
y = 3.14            # float
name = "Alice"      # str
is_valid = True     # bool
nothing = None      # NoneType

# Multiple assignment
a, b, c = 1, 2, 3
x = y = z = 0

# Type conversion
int("42")           # 42
float("3.14")       # 3.14
str(123)            # "123"
bool(1)             # True
```

**Truthy/Falsy Values**
- Falsy: `False`, `None`, `0`, `0.0`, `""`, `[]`, `{}`, `()`
- Everything else is truthy

## Operators

```python
# Arithmetic
+ - * / // % **     # add, sub, mul, div, floor_div, modulo, power

# Comparison
== != < > <= >=     # equality, inequality, relational

# Logical
and or not          # logical operators

# Membership & Identity
in, not in          # membership test
is, is not          # identity test (same object)

# Augmented assignment
+= -= *= /= //= %= **=
```

## Strings

```python
s = "hello"
s = 'hello'         # single or double quotes
s = """multiline
string"""           # triple quotes

# f-strings (formatted string literals, Python 3.6+)
name, age = "Bob", 30
f"Name: {name}, Age: {age}"           # "Name: Bob, Age: 30"
f"{age * 2}"                          # "60"

# Common methods
s.upper()           # "HELLO"
s.lower()           # "hello"
s.strip()           # remove leading/trailing whitespace
s.split(",")        # split into list
",".join(["a","b"]) # "a,b"
s.replace("h","j")  # "jello"
s.startswith("he")  # True
s.endswith("lo")    # True
s.find("ll")        # 2 (index, -1 if not found)
s[0]                # "h" (indexing)
s[1:4]              # "ell" (slicing)
len(s)              # 5
```

## Lists (mutable, ordered)

```python
lst = [1, 2, 3, 4]
lst = []            # empty list

# Indexing & slicing
lst[0]              # 1 (first element)
lst[-1]             # 4 (last element)
lst[1:3]            # [2, 3] (slice from index 1 to 3, exclusive)
lst[:2]             # [1, 2] (first two)
lst[2:]             # [3, 4] (from index 2 to end)
lst[::2]            # [1, 3] (every 2nd element)

# Methods
lst.append(5)       # add to end
lst.extend([6,7])   # add multiple elements
lst.insert(0, 0)    # insert at index
lst.remove(3)       # remove first occurrence
lst.pop()           # remove & return last element
lst.pop(0)          # remove & return element at index
lst.sort()          # sort in place
lst.reverse()       # reverse in place
lst.index(2)        # find index of value
lst.count(2)        # count occurrences
lst.clear()         # remove all elements

# Built-in functions
len(lst)            # length
sorted(lst)         # return sorted copy
reversed(lst)       # return reversed iterator
min(lst), max(lst)  # min/max values
sum(lst)            # sum of elements

# List comprehension
[x*2 for x in range(5)]                    # [0, 2, 4, 6, 8]
[x for x in range(10) if x % 2 == 0]       # [0, 2, 4, 6, 8]
```

## Tuples (immutable, ordered)

```python
tup = (1, 2, 3)
tup = 1, 2, 3       # parentheses optional
tup = (1,)          # single element tuple (note comma)

# Indexing/slicing same as lists
tup[0]              # 1
tup[1:]             # (2, 3)

# Unpacking
x, y, z = tup       # x=1, y=2, z=3
x, *rest = tup      # x=1, rest=[2,3]
```

## Sets (mutable, unordered, unique elements)

```python
s = {1, 2, 3}
s = set()           # empty set (not {})

# Methods
s.add(4)            # add element
s.remove(2)         # remove (raises error if not found)
s.discard(2)        # remove (no error if not found)
s.clear()           # remove all

# Set operations
a = {1, 2, 3}
b = {2, 3, 4}
a | b               # {1, 2, 3, 4} (union)
a & b               # {2, 3} (intersection)
a - b               # {1} (difference)
a ^ b               # {1, 4} (symmetric difference)

# Set comprehension
{x*2 for x in range(5)}  # {0, 2, 4, 6, 8}
```

## Dictionaries (mutable, key-value pairs)

```python
d = {"name": "Alice", "age": 30}
d = {}              # empty dict
d = dict(name="Alice", age=30)  # alternative

# Access
d["name"]           # "Alice"
d.get("name")       # "Alice"
d.get("city", "N/A")  # "N/A" (default if key not found)

# Modify
d["age"] = 31       # update
d["city"] = "NYC"   # add new key

# Methods
d.keys()            # dict_keys(['name', 'age'])
d.values()          # dict_values(['Alice', 30])
d.items()           # dict_items([('name', 'Alice'), ('age', 30)])
d.pop("age")        # remove & return value
d.update({"x": 1})  # merge another dict
d.clear()           # remove all

# Check membership
"name" in d         # True (checks keys)

# Dict comprehension
{x: x*2 for x in range(5)}  # {0: 0, 1: 2, 2: 4, 3: 6, 4: 8}
```

## Control Flow

### If/Elif/Else

```python
if condition:
    # code
elif another_condition:
    # code
else:
    # code

# Ternary operator
x = value_if_true if condition else value_if_false
```

### For Loops

```python
for item in iterable:
    # code

for i in range(5):          # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 5):       # 2, 3, 4
    print(i)

for i in range(0, 10, 2):   # 0, 2, 4, 6, 8 (step=2)
    print(i)

# Enumerate (index + value)
for i, val in enumerate(lst):
    print(i, val)

# Zip (parallel iteration)
for x, y in zip(list1, list2):
    print(x, y)

# Loop control
break       # exit loop
continue    # skip to next iteration
```

### While Loops

```python
while condition:
    # code
    if exit_condition:
        break
```

## Functions

```python
def function_name(param1, param2):
    """Docstring describing function"""
    # code
    return result

# Default arguments
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}"

# Variable arguments
def sum_all(*args):         # args is a tuple
    return sum(args)

def print_info(**kwargs):   # kwargs is a dict
    for key, val in kwargs.items():
        print(f"{key}: {val}")

# Lambda (anonymous function)
square = lambda x: x * 2
add = lambda x, y: x + y

# Map, filter, reduce
list(map(lambda x: x*2, [1,2,3]))       # [2, 4, 6]
list(filter(lambda x: x>2, [1,2,3,4]))  # [3, 4]

from functools import reduce
reduce(lambda x,y: x+y, [1,2,3,4])      # 10
```

## Classes & OOP

```python
class ClassName:
    """Class docstring"""
    
    # Class variable (shared by all instances)
    class_var = 0
    
    def __init__(self, param1, param2):
        """Constructor"""
        self.param1 = param1      # instance variable
        self.param2 = param2
    
    def method(self):
        """Instance method"""
        return self.param1
    
    @classmethod
    def class_method(cls):
        """Class method (receives class, not instance)"""
        return cls.class_var
    
    @staticmethod
    def static_method():
        """Static method (no self or cls)"""
        return "static"
    
    def __str__(self):
        """String representation"""
        return f"ClassName({self.param1})"

# Instantiation
obj = ClassName("value1", "value2")
obj.method()
ClassName.class_method()

# Inheritance
class Child(Parent):
    def __init__(self, param1, param2, param3):
        super().__init__(param1, param2)  # call parent constructor
        self.param3 = param3
    
    def method(self):
        """Override parent method"""
        return super().method() + " modified"
```

## Exception Handling

```python
try:
    # code that might raise exception
    result = 10 / 0
except ZeroDivisionError as e:
    # handle specific exception
    print(f"Error: {e}")
except (TypeError, ValueError):
    # handle multiple exceptions
    print("Type or Value error")
except Exception as e:
    # catch all other exceptions
    print(f"Unexpected error: {e}")
else:
    # executes if no exception raised
    print("Success")
finally:
    # always executes
    print("Cleanup")

# Raise exception
raise ValueError("Invalid value")
raise Exception("Custom error message")

# Common exceptions
# ValueError, TypeError, KeyError, IndexError, AttributeError
# FileNotFoundError, ZeroDivisionError, ImportError
```

## File I/O

```python
# Read file
with open("file.txt", "r") as f:
    content = f.read()              # read entire file
    # or
    lines = f.readlines()           # list of lines
    # or
    for line in f:                  # iterate line by line
        print(line.strip())

# Write file
with open("file.txt", "w") as f:
    f.write("Hello\n")
    f.writelines(["line1\n", "line2\n"])

# Append to file
with open("file.txt", "a") as f:
    f.write("Appended text\n")

# Modes: "r" (read), "w" (write), "a" (append), "r+" (read/write)
# Add "b" for binary mode: "rb", "wb"
```

## Modules & Packages

```python
# Import entire module
import math
math.sqrt(16)

# Import with alias
import numpy as np
np.array([1, 2, 3])

# Import specific items
from math import sqrt, pi
sqrt(16)

# Import all (not recommended)
from math import *

# Import from package
from package.module import function

# Check what's in a module
dir(math)           # list all attributes
help(math.sqrt)     # documentation
```

## Common Built-in Functions

```python
# Type & conversion
type(x)             # get type
isinstance(x, int)  # check type
int(), float(), str(), bool(), list(), tuple(), set(), dict()

# Iteration
range(start, stop, step)
enumerate(iterable, start=0)
zip(iter1, iter2, ...)
reversed(sequence)
sorted(iterable, key=None, reverse=False)

# Aggregation
len(obj)
sum(iterable, start=0)
min(iterable), max(iterable)
all(iterable)       # True if all elements truthy
any(iterable)       # True if any element truthy

# I/O
print(*objects, sep=' ', end='\n')
input(prompt)       # read string from stdin

# Other
abs(x)              # absolute value
round(x, n)         # round to n decimals
pow(x, y)           # x to power y
divmod(x, y)        # (x//y, x%y)
```

## Comprehensions Summary

```python
# List comprehension
[expr for item in iterable if condition]

# Dict comprehension
{key_expr: val_expr for item in iterable if condition}

# Set comprehension
{expr for item in iterable if condition}

# Generator expression (memory efficient)
(expr for item in iterable if condition)
```

## Useful Idioms

```python
# Swap variables
a, b = b, a

# Chained comparison
if 0 < x < 10:
    pass

# Multiple conditions with all/any
if all([cond1, cond2, cond3]):
    pass

# Default dict value
count = counts.get(key, 0)
counts[key] = count + 1

# Iterate with index
for i, item in enumerate(items):
    print(i, item)

# Unpack with remainder
first, *middle, last = [1, 2, 3, 4, 5]  # first=1, middle=[2,3,4], last=5

# Dictionary merge (Python 3.9+)
merged = dict1 | dict2

# Walrus operator (Python 3.8+, assignment in expression)
if (n := len(items)) > 10:
    print(f"List has {n} items")

# Check emptiness (Pythonic)
if not lst:         # instead of if len(lst) == 0:
    print("empty")
```

## Common Patterns

```python
# Safe dictionary access
value = d.get(key, default_value)

# Count occurrences
from collections import Counter
counts = Counter([1, 2, 2, 3, 3, 3])  # Counter({3: 3, 2: 2, 1: 1})

# Default dictionary
from collections import defaultdict
dd = defaultdict(list)
dd['key'].append(1)  # no KeyError

# Named tuples
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(1, 2) 
p.x  # 1

# Context manager (with statement)
with resource as r:
    # resource automatically cleaned up
    pass
```  
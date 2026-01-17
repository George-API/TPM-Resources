# Python Patterns & Best Practices

**Focus**: Common patterns, idioms, and best practices for Python development - supplements syntax reference and DSA guide.

## Table of Contents

- [Comprehensions](#comprehensions)
- [Iteration Patterns](#iteration-patterns)
- [Dictionary Patterns](#dictionary-patterns)
- [Error Handling Patterns](#error-handling-patterns)
- [Context Managers](#context-managers)
- [Decorators](#decorators)
- [Function Patterns](#function-patterns)
- [Class Patterns](#class-patterns)
- [Data Transformation](#data-transformation)
- [Useful Idioms](#useful-idioms)

---

## Comprehensions

### List Comprehension
- `[expr for item in iterable]` - transform elements
- `[expr for item in iterable if condition]` - filter + transform
- `[expr for item in iterable for subitem in item]` - nested (flatten)
- `[x*2 for x in range(5) if x % 2 == 0]` - filter then transform

### Dict Comprehension
- `{key: value for item in iterable}` - create dict
- `{k: v for k, v in items if condition}` - filter key-value pairs
- `{k: v for d in dicts for k, v in d.items()}` - merge multiple dicts

### Set Comprehension
- `{expr for item in iterable}` - create set (unique values)
- `{x for x in items if condition}` - filter to set

### Generator Expression
- `(expr for item in iterable)` - memory-efficient iteration
- `sum(x*2 for x in range(1000))` - use in functions that consume iterables
- `max((x, i) for i, x in enumerate(items))` - find max with index

### Nested Comprehensions
- `[[i*j for j in range(3)] for i in range(3)]` - 2D list
- `[x for row in matrix for x in row]` - flatten 2D list

---

## Iteration Patterns

### Enumerate
- `for i, item in enumerate(items):` - iterate with index
- `for i, item in enumerate(items, start=1):` - start from 1

### Zip
- `for x, y in zip(list1, list2):` - parallel iteration
- `for x, y, z in zip(a, b, c):` - multiple iterables
- `list(zip(*matrix))` - transpose matrix (unpacking)

### Itertools
- `from itertools import chain; chain(list1, list2)` - chain iterables
- `from itertools import groupby; groupby(sorted(items), key)` - group consecutive items
- `from itertools import combinations, permutations` - combinations/permutations

### Unpacking
- `first, *rest = items` - unpack with remainder
- `*args, last = items` - unpack from end
- `a, (b, c) = [1, [2, 3]]` - nested unpacking
- `d1, d2 = {**d1, **d2}, {}` - merge dicts (Python 3.5+)

---

## Dictionary Patterns

### Safe Access
- `value = d.get(key, default)` - get with default
- `value = d.setdefault(key, default)` - get or set default
- `value = d.pop(key, default)` - get and remove

### Frequency Counting
- `from collections import Counter; counts = Counter(items)` - count occurrences
- `counts.most_common(k)` - top-k most frequent
- `d[key] = d.get(key, 0) + 1` - manual counting

### Grouping
- `from collections import defaultdict; dd = defaultdict(list)` - group by key
- `dd[key].append(value)` - no KeyError
- `{k: [x for x in items if condition(x)] for k in keys}` - dict comprehension grouping

### Merging
- `merged = {**d1, **d2}` - merge (Python 3.5+)
- `merged = d1 | d2` - union operator (Python 3.9+)
- `d1 |= d2` - in-place merge (Python 3.9+)

### Dictionary Views
- `d.keys()`, `d.values()`, `d.items()` - return views (update with dict)
- `list(d.keys())` - convert to list if needed

---

## Error Handling Patterns

### Try-Except
- `try: ... except SpecificError: ...` - catch specific exceptions
- `try: ... except (TypeError, ValueError): ...` - catch multiple
- `try: ... except Exception as e: ...` - catch all, access error
- `try: ... except: ... else: ... finally: ...` - full pattern

### Exception Chaining
- `raise RuntimeError("msg") from e` - chain exceptions (Python 3+)
- `raise ValueError("msg") from None` - suppress original traceback

### Context Managers for Errors
- `with open(file) as f: ...` - auto-close file
- `from contextlib import suppress; with suppress(KeyError): ...` - suppress specific errors

### Validation Patterns
- `if not condition: raise ValueError("msg")` - fail fast
- `assert condition, "msg"` - debug assertions (can be disabled)
- `value = arg if arg else default` - provide defaults

---

## Context Managers

### Built-in
- `with open(file, 'r') as f: ...` - file handling
- `with open(file1) as f1, open(file2) as f2: ...` - multiple files

### Custom Context Manager
```python
from contextlib import contextmanager

@contextmanager
def timer():
    start = time.time()
    try:
        yield
    finally:
        print(f"Elapsed: {time.time() - start}")

# Usage
with timer():
    # code
    pass
```

### Contextlib Utilities
- `from contextlib import redirect_stdout; with redirect_stdout(file): ...` - redirect stdout
- `from contextlib import nullcontext; with nullcontext(): ...` - no-op context

---

## Decorators

### Function Decorator
```python
def decorator(func):
    def wrapper(*args, **kwargs):
        # before
        result = func(*args, **kwargs)
        # after
        return result
    return wrapper

@decorator
def func():
    pass
```

### Decorator with Arguments
```python
def decorator(arg):
    def inner(func):
        def wrapper(*args, **kwargs):
            # use arg
            return func(*args, **kwargs)
        return wrapper
    return inner

@decorator("value")
def func():
    pass
```

### functools.wraps
```python
from functools import wraps

def decorator(func):
    @wraps(func)  # preserves function metadata
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### Common Decorators
- `@functools.lru_cache(maxsize=None)` - memoization
- `@functools.singledispatch` - function overloading
- `@property` - computed attributes
- `@classmethod`, `@staticmethod` - class/static methods

---

## Function Patterns

### Default Arguments
- `def func(arg=None): if arg is None: arg = []` - avoid mutable defaults
- `def func(arg=object()): if arg is sentinel: arg = []` - sentinel pattern

### Variable Arguments
- `def func(*args, **kwargs): ...` - flexible arguments
- `def func(a, b, *, kw_only): ...` - keyword-only (Python 3+)
- `def func(pos_only, /, normal, *, kw_only): ...` - positional-only (Python 3.8+)

### Function Composition
- `f(g(x))` - nested calls
- `from functools import reduce; reduce(lambda f, g: lambda x: g(f(x)), funcs)` - compose multiple

### Partial Application
- `from functools import partial; add_10 = partial(add, 10)` - fix arguments
- `from functools import partialmethod` - partial for methods

---

## Class Patterns

### Dataclasses
- `from dataclasses import dataclass; @dataclass class Point: x: int; y: int` - auto-generate __init__, __repr__, __eq__
- `@dataclass(frozen=True)` - immutable dataclass
- `@dataclass(order=True)` - enable comparison operators

### Named Tuples
- `from collections import namedtuple; Point = namedtuple('Point', ['x', 'y'])` - immutable, lightweight
- `p = Point(1, 2); p.x, p[0]` - access by name or index

### Slots
- `class Optimized: __slots__ = ('x', 'y')` - memory optimization (no __dict__)

### Property Pattern
```python
class C:
    @property
    def value(self):
        return self._value
    
    @value.setter
    def value(self, val):
        self._value = val
```

### Singleton Pattern
```python
class Singleton:
    _instance = None
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```

---

## Data Transformation

### Filtering
- `filtered = [x for x in items if condition(x)]` - list comprehension
- `filtered = filter(lambda x: condition(x), items)` - filter function
- `filtered = list(filter(None, items))` - remove falsy values

### Mapping
- `mapped = [f(x) for x in items]` - list comprehension
- `mapped = map(f, items)` - map function
- `mapped = list(map(str.upper, strings))` - apply method

### Reducing
- `from functools import reduce; result = reduce(lambda x, y: x + y, items)` - accumulate
- `sum(items)`, `max(items)`, `min(items)` - built-in reducers

### Sorting
- `sorted(items, key=lambda x: x.field)` - sort by key
- `sorted(items, key=lambda x: (x.field1, -x.field2))` - multiple keys
- `items.sort(reverse=True)` - in-place sort

### Grouping
- `from itertools import groupby; {k: list(g) for k, g in groupby(sorted(items), key)}` - group consecutive
- `from collections import defaultdict; dd = defaultdict(list); [dd[k].append(v) for k, v in pairs]` - group by key

---

## Useful Idioms

### Variable Swapping
- `a, b = b, a` - swap without temp

### Chained Comparisons
- `if 0 < x < 10:` - equivalent to `0 < x and x < 10`

### Multiple Conditions
- `if all([cond1, cond2, cond3]):` - all must be true
- `if any([cond1, cond2, cond3]):` - any must be true

### Walrus Operator (Python 3.8+)
- `if (n := len(items)) > 10: ...` - assignment in expression
- `[y for x in data if (y := process(x)) > 0]` - in comprehensions
- `while (line := file.readline()): ...` - in while loops

### Emptiness Checks
- `if not lst:` - Pythonic (instead of `len(lst) == 0`)
- `if lst:` - check if non-empty

### String Joining
- `",".join(items)` - join list to string (items must be strings)
- `" ".join(map(str, numbers))` - join numbers

### Dictionary Defaults
- `value = d.get(key, default)` - safe access
- `value = d.setdefault(key, default)` - get or set

### Unpacking
- `first, *rest = items` - unpack with remainder
- `*args, last = items` - unpack from end
- `a, (b, c) = nested` - nested unpacking

### Type Checking
- `isinstance(obj, (int, str))` - check multiple types
- `type(obj) is int` - exact type (not subclass)

### Truthiness
- Falsy: `False`, `None`, `0`, `0.0`, `""`, `[]`, `{}`, `()`
- Everything else is truthy

---

## Best Practices

### Naming
- `snake_case` for functions, variables
- `PascalCase` for classes
- `UPPER_CASE` for constants
- `_private` for internal (single underscore)
- `__special__` for special methods

### Imports
- `import standard_library` - standard library
- `from third_party import specific` - third-party
- `from local import module` - local imports
- Group: stdlib, third-party, local (blank line between)

### Documentation
- `"""Docstring"""` - use docstrings, not comments
- `# Inline comment` - explain why, not what
- Type hints for function signatures (Python 3.5+)

### Performance
- Use `set` for membership testing (O(1) vs O(n) for list)
- Use generators for large datasets
- `collections.deque` for queue operations
- `f-strings` for string formatting (Python 3.6+)

### Error Handling
- Catch specific exceptions, not bare `except:`
- Use `try/except` for expected errors
- Fail fast with validation
- Log errors appropriately

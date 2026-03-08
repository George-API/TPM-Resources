# C# Syntax & Fundamentals

**Focus**: Core C# syntax, types, operators, control flow, OOP, LINQ, async/await, collections, exception handling.

## Table of Contents

- [Type System & Memory](#type-system--memory)
- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Control Flow](#control-flow)
- [Functions & Methods](#functions--methods)
- [Generics](#generics)
- [Extension Methods](#extension-methods)
- [Attributes](#attributes)
- [Events & Delegates](#events--delegates)
- [Tuples](#tuples)
- [Classes & OOP](#classes--oop)
- [Collections & LINQ](#collections--linq)
- [Exception Handling](#exception-handling)
- [Async/Await](#asyncawait)
- [File I/O](#file-io)
- [JSON Payloads](#json-payloads)
- [Common Patterns](#common-patterns)

---

## Type System & Memory

**Value types** (stack-allocated): `int`, `long`, `double`, `float`, `decimal`, `bool`, `char`, `byte`, `short`, `struct` - Stored on stack, copied by value, no null (unless nullable `int?`)

**Reference types** (heap-allocated): `string`, `object`, `class`, `array`, `interface` - Stored on heap, copied by reference, can be null

**Memory management**: Automatic garbage collection (GC) - No manual `free()` like C/C++. GC automatically reclaims unused heap memory

**Stack vs Heap**: Value types and references go on stack; actual objects go on heap. References point to heap objects

**Boxing/Unboxing**: `int x = 5; object obj = x;` (boxing - value to reference), `int y = (int)obj;` (unboxing - reference to value) - Performance cost, avoid in hot paths

**Pass by value vs reference**: `void Method(int x)` (value copied), `void Method(ref int x)` (reference passed), `void Method(out int x)` (must assign, reference passed)

**String immutability**: `string` is immutable - `str += "text"` creates new string object. Use `StringBuilder` for multiple concatenations

**Nullable reference types** (C# 8+): `string?` (can be null), `string` (non-nullable) - Compiler warnings for null safety

**Compilation**: C# compiles to IL (Intermediate Language), runs on .NET runtime (CLR) - JIT compilation to native code

---

## Built-In Syntax

### Access modifiers
- `public` - accessible from anywhere
- `private` - accessible only within class
- `protected` - accessible within class and derived classes
- `internal` - accessible within assembly
- `protected internal` - accessible within assembly or derived classes

### Class/interface keywords
- `class` - define class
- `interface` - define interface
- `abstract` - abstract class/method
- `sealed` - cannot be inherited
- `static` - belongs to type, not instance
- `partial` - split class across files
- `record` - immutable data class (C# 9+)

### Method/field modifiers
- `void` - no return value
- `return` - return value from method
- `this` - reference to current object
- `base` - reference to parent class
- `static` - type-level member
- `readonly` - can only be assigned in constructor
- `const` - compile-time constant

### Control flow
- `if` - conditional branch
- `else` - default branch
- `switch` - multi-way branch (pattern matching C# 8+)
- `case` - switch case label
- `default` - switch default case
- `for` - iteration loop
- `foreach` - iterate over collection
- `while` - loop while condition true
- `do` - do-while loop
- `break` - exit loop/switch
- `continue` - skip to next iteration

### Exception handling
- `try` - start exception handling
- `catch` - catch exception
- `finally` - always execute cleanup
- `throw` - throw exception
- `throw` expression - throw in expression context (C# 7+)

### Other keywords
- `using` - import namespace / dispose pattern
- `namespace` - declare namespace
- `new` - create object instance
- `is` - type check / pattern matching
- `as` - safe type cast (returns null on failure)
- `null` - null reference
- `var` - local variable type inference
- `async` - asynchronous method
- `await` - await asynchronous operation

---

## Variables & Basic Types

- **Primitives** (value types): `int` (32-bit), `long` (64-bit, suffix `L`), `double` (64-bit), `float` (32-bit, suffix `f`), `decimal` (128-bit, suffix `m`), `bool`, `char` (16-bit Unicode), `byte` (8-bit), `short` (16-bit)
- **Reference types**: `string` (immutable, UTF-16), `object` (base type), `dynamic` (runtime type checking)
- **Nullable value types**: `int? x = null;` (value types can be null), `bool? flag = null;`
- **Type conversion**: `(int) 3.14` (explicit cast), `double y = 5` (implicit widening), `Convert.ToInt32("42")`, `int.Parse("42")` (throws on failure), `int.TryParse("42", out int result)` (returns bool)
- **Type checking**: `obj is string`, `obj is string s` (pattern matching), `obj as string` (returns null on failure), `(string)obj` (throws on failure)

---

## Operators

- **Arithmetic**: `+ - * / % ++ --`
- **Comparison**: `== != < > <= >=`, `is` (type check), `is not` (C# 9+)
- **Logical**: `&& || !`
- **Bitwise**: `& | ^ ~ << >>`
- **Assignment**: `= += -= *= /= %=`
- **Ternary**: `condition ? valueIfTrue : valueIfFalse`
- **Null-coalescing**: `x ?? y` (returns y if x is null), `x ??= y` (assign if null, C# 8+)
- **Null-conditional**: `obj?.Property`, `obj?.Method()`, `arr?[index]` (C# 6+)

---

## Control Flow

- **If/Else**: `if (cond) { } else if (cond) { } else { }`
- **Ternary**: `int max = (a > b) ? a : b;`
- **Switch**: `switch (val) { case 1: break; default: break; }` (traditional), `switch (val) { case 1 => "one"; _ => "other"; }` (expression, C# 8+)
- **Pattern matching**: `if (obj is string s) { }` (C# 7+), `switch (obj) { case string s: break; }`
- **For**: `for (int i = 0; i < 10; i++) { }`
- **Foreach**: `foreach (var item in items) { }`
- **While**: `while (cond) { }`, `do { } while (cond);`

---

## Functions & Methods

- **Definition**: `public static int Add(int a, int b) => a + b;` (expression body, C# 6+)
- **Overloading**: Same method name, different parameters
- **Optional parameters**: `void Method(int x, int y = 0) { }`
- **Named arguments**: `Method(x: 1, y: 2);`
- **Params**: `int Sum(params int[] numbers) { }` (variable arguments)
- **Local functions**: `void Outer() { int Local() => 42; }` (C# 7+)
- **Lambda expressions**: `Func<int, int> square = x => x * x;`, `Action<string> print = s => Console.WriteLine(s);`
- **Expression-bodied members**: `public int GetValue() => _value;` (C# 6+)

---

## Generics

**Generic classes**: `public class Repository<T> { public T GetById(int id) { } }` - Type parameter `T` can be any type

**Generic methods**: `public T Process<T>(T item) { return item; }` - Type inference: `Process(42)` (T = int)

**Generic constraints**: `where T : class` (reference type), `where T : struct` (value type), `where T : new()` (has constructor), `where T : IService` (implements interface)

**Common generics**: `List<T>`, `Dictionary<TKey, TValue>`, `Task<T>`, `Nullable<T>` (T?), `Func<T, TResult>`, `Action<T>`

**Generic interfaces**: `IEnumerable<T>`, `IComparable<T>`, `IEquatable<T>` - Type-safe collections and comparisons

**Variance**: `IEnumerable<out T>` (covariant), `Action<in T>` (contravariant) - C# 4+

---

## Extension Methods

**Extension method syntax**:
```csharp
public static class StringExtensions
{
    public static bool IsNullOrEmpty(this string? str) => string.IsNullOrEmpty(str);
}

// Usage: "".IsNullOrEmpty() - Extends string type
```

**Extension method pattern**: `public static ReturnType MethodName(this ExtendedType obj, params...)` - Must be in static class, first parameter uses `this`

**Common use cases**: Fluent APIs, adding methods to sealed types, LINQ-style operations

**LINQ as extensions**: `Where()`, `Select()`, `First()` are extension methods on `IEnumerable<T>`

---

## Attributes

**Attribute syntax**: `[Obsolete("Use NewMethod instead")]`, `[Serializable]`, `[Authorize]` - Metadata for types, members, parameters

**Custom attributes**:
```csharp
[AttributeUsage(AttributeTargets.Method | AttributeTargets.Class)]
public class LogAttribute : Attribute
{
    public string Level { get; set; }
}

[Log(Level = "Info")]
public void Method() { }
```

**Common attributes**: `[Obsolete]`, `[Serializable]`, `[Conditional]`, `[DllImport]`, `[AttributeUsage]` - Framework and custom metadata

**Reflection**: `typeof(MyClass).GetCustomAttributes()`, `methodInfo.GetCustomAttribute<LogAttribute>()` - Access attributes at runtime

**ASP.NET Core attributes**: `[HttpGet]`, `[Authorize]`, `[FromBody]`, `[ApiController]` - Framework-specific attributes

---

## Events & Delegates

**Delegate**: `public delegate void EventHandler(object sender, EventArgs e);` - Type-safe function pointer

**Action/Func**: `Action<string> print = s => Console.WriteLine(s);`, `Func<int, int, int> add = (a, b) => a + b;` - Built-in delegate types

**Events**:
```csharp
public event EventHandler<CustomEventArgs> SomethingHappened;

protected virtual void OnSomethingHappened(CustomEventArgs e)
{
    SomethingHappened?.Invoke(this, e);
}
```

**Event pattern**: Declare event, raise via protected method, subscribe with `+=`, unsubscribe with `-=`

**Event handler**: `obj.SomethingHappened += (sender, e) => { };` - Subscribe to events

**Use cases**: Observer pattern, UI events, pub/sub patterns, decoupled communication

---

## Tuples

**Tuple syntax**: `(int x, string y) = (1, "hello");`, `var point = (X: 1, Y: 2);` - Lightweight grouping of values

**Named tuples**: `(string Name, int Age) person = ("John", 30);` - Access via `person.Name`, `person.Age`

**Tuple return**: `(string, int) GetPerson() => ("John", 30);` - Multiple return values without custom type

**Tuple deconstruction**: `var (name, age) = GetPerson();` - Unpack tuple into variables

**Tuple types**: `ValueTuple<T1, T2>` (value type, C# 7+), `Tuple<T1, T2>` (reference type, legacy)

**Use cases**: Multiple return values, temporary grouping, avoiding custom DTOs for simple cases

---

## Classes & OOP

- **Class** (reference type): `public class User { private string _name; public User(string name) => _name = name; public string Name { get; set; } }`
- **Struct** (value type): `public struct Point { public int X; public int Y; }` - Stack-allocated, no inheritance, use for small data (typically < 16 bytes)
- **Properties**: `public string Name { get; set; }` (auto-property), `public string Name { get; private set; }` (private setter), `public int Age { get; init; }` (init-only, C# 9+), `public string Name { get { return _name; } set { _name = value; } }` (custom)
- **Records** (C# 9+): `public record UserEvent(string UserId, string Action, DateTime Timestamp);` (immutable, value equality, reference type on heap)

**Required members** (C# 11+): `public required string Name { get; set; }` - Must be initialized

**Primary constructors** (C# 12+): `public class User(string name, string email) { }` - Constructor parameters available throughout class
- **Inheritance**: `public class Admin : User { public Admin(string name) : base(name) { } public override void Greet() { base.Greet(); } }` - Single inheritance only (unlike C++)
- **Interface**: `public interface IService { void Execute(); }` (default implementations C# 8+), `void Method() { }` (default implementation)
- **Abstract class**: `public abstract class BaseHandler { public abstract void Handle(); protected virtual void Validate() { } }` - Cannot instantiate, can have implementation
- **Virtual/Override**: `virtual` (can be overridden), `override` (overrides base), `new` (hides base member)

---

## Collections & LINQ

- **List** (dynamic array): `List<string> list = new();` (target-typed new, C# 9+), `list.Add("item");`, `list[0]`, `list.Count`, `list.Remove("item")`
- **Dictionary** (hash table): `Dictionary<string, string> dict = new();`, `dict["key"] = "value";`, `dict.TryGetValue("key", out var value);`, `dict.ContainsKey("key")`
- **HashSet** (unique values): `HashSet<int> set = new();`, `set.Add(1);`, `set.Contains(1);`
- **Array** (fixed size): `int[] arr = {1, 2, 3};`, `int[] arr2 = new int[10];`, `arr.Length` (immutable size)
- **LINQ** (deferred execution): `items.Where(x => x > 0).Select(x => x * 2).ToList();` - Query executes when enumerated (`.ToList()`, `.ToArray()`, `foreach`)
- **LINQ methods**: `Where()` (filter), `Select()` (transform), `First()`, `FirstOrDefault()`, `Any()`, `All()`, `Count()`, `Sum()`, `Average()`, `GroupBy()`, `OrderBy()`, `Join()`, `Distinct()`, `Skip()`, `Take()`
- **Query syntax**: `from x in items where x > 0 select x * 2;` (alternative to method syntax)

---

## Exception Handling

- **Try-catch-finally**: `try { } catch (SpecificException ex) { } catch (Exception ex) { } finally { }`
- **Using statement**: `using (var stream = new FileStream(...)) { }` (auto-dispose), `using var stream = new FileStream(...);` (C# 8+)
- **Throw**: `throw new ArgumentException("message");`
- **Custom**: `class IntegrationException : Exception { public IntegrationException(string msg, Exception inner) : base(msg, inner) { } }`
- **Exception filters**: `catch (Exception ex) when (ex.Message.Contains("timeout")) { }` (C# 6+)

---

## Async/Await

- **Async method**: `public async Task<string> GetDataAsync() { return await httpClient.GetStringAsync(url); }`
- **Task**: `Task<T>` (returns value), `Task` (no return), `ValueTask<T>` (lightweight, C# 7+)
- **Await**: `var result = await SomeAsyncMethod();`
- **ConfigureAwait**: `await task.ConfigureAwait(false);` (avoid context capture)
- **Parallel**: `await Task.WhenAll(tasks);`, `await Task.WhenAny(tasks);`
- **Cancellation**: `CancellationTokenSource cts = new();`, `await MethodAsync(cts.Token);`
- **IAsyncEnumerable** (async streams, C# 8+): `public async IAsyncEnumerable<int> GetNumbersAsync() { yield return i; }`, `await foreach (var item in GetNumbersAsync()) { }`

---

## File I/O

- **Read** (small): `string content = await File.ReadAllTextAsync("file.txt");`
- **Read** (lines): `string[] lines = await File.ReadAllLinesAsync("file.txt");`
- **Write**: `await File.WriteAllTextAsync("file.txt", content);`, `await File.WriteAllLinesAsync("file.txt", lines);`
- **Stream**: `using var stream = new FileStream("file.txt", FileMode.Open);`, `using var reader = new StreamReader(stream);`

---

## JSON Payloads (System.Text.Json)

- **Setup**: `JsonSerializerOptions options = new() { PropertyNamingPolicy = JsonNamingPolicy.CamelCase };`
- **Parse**: `var obj = JsonSerializer.Deserialize<MyClass>(json, options);`
- **Serialize**: `string json = JsonSerializer.Serialize(obj, options);`
- **DTO**: `public record UserEvent(string UserId, string Action, DateTime Timestamp);`
- **Attributes**: `[JsonPropertyName("user_id")]` (custom property names)

---

## Common Patterns

**Null handling**: `var value = obj?.Property ?? defaultValue;` (null-coalescing), `if (obj is not null) { }`, `obj?.Method()` (null-conditional), `obj ?? throw new ArgumentNullException()` (throw if null)

**String operations**: `string.IsNullOrWhiteSpace(str)`, `str.Contains("text")`, `str.StartsWith("prefix")`, `str.Replace("old", "new")`, `string.Join(", ", items)`, `$"Hello {name}"` (interpolation), `str.Substring(start, length)`, `str.Split(',')`

**StringBuilder** (mutable strings): `var sb = new StringBuilder(); sb.Append("text"); sb.ToString();` - Use for multiple concatenations

**Collections**: `list.Add(item)`, `list.Remove(item)`, `list.Contains(item)`, `dict.TryGetValue(key, out var value)`, `dict.ContainsKey(key)`, `dict.Remove(key)`

**LINQ common**: `items.Where(x => x > 0)`, `items.Select(x => x * 2)`, `items.First()`, `items.FirstOrDefault()`, `items.Any()`, `items.All(x => x > 0)`, `items.Count()`, `items.OrderBy(x => x.Property)`, `items.GroupBy(x => x.Category)`, `items.Skip(n).Take(m)` (pagination)

**Disposal pattern**: `using var resource = new Resource();` (auto-dispose, C# 8+), `using (var resource = new Resource()) { }` (traditional), `IDisposable` interface for unmanaged resources

**IDisposable**: `public class MyClass : IDisposable { public void Dispose() { /* cleanup */ } }` - For file handles, network connections, etc.

**Ref/Out parameters**: `void Method(ref int x)` (pass by reference, must be initialized), `void Method(out int x)` (must assign, used for multiple returns)

**Pattern matching** (C# 7+): `if (obj is string s) { }`, `switch (obj) { case string s: break; case int i when i > 0: break; }`

**Deconstruction**: `var (x, y) = point;` (tuple deconstruction), `var (name, age) = GetPerson();`

**IEnumerable/IEnumerator**: `IEnumerable<T>` (iterable), `IEnumerator<T>` (iterator) - Core interfaces for collections, enables `foreach`

**Yield return**: `yield return item;` (lazy evaluation), `yield break;` (exit) - Custom iterators without full IEnumerator implementation

---

> **Note**: C# is a managed language (automatic GC, no manual memory management) with strong typing and modern features (C# 9+). Value types (primitives, structs) are stack-allocated and copied by value. Reference types (classes, strings, arrays) are heap-allocated and copied by reference. Prefer async/await for I/O, use LINQ for data transformations, leverage nullable reference types for null safety, and use records for immutable data. C# compiles to IL and runs on .NET runtime (JIT compilation).


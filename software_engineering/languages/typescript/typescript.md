# TypeScript Quick Reference Guide

## Table of Contents

- [Basic Types](#basic-types)
- [Variables & Declarations](#variables--declarations)
- [Functions](#functions)
- [Arrays & Tuples](#arrays--tuples)
- [Objects & Interfaces](#objects--interfaces)
- [Classes](#classes)
- [Enums](#enums)
- [Unions & Intersections](#unions--intersections)
- [Generics](#generics)
- [Type Utilities](#type-utilities)
- [Control Flow](#control-flow)
- [Modules & Imports](#modules--imports)
- [Error Handling](#error-handling)
- [Common Patterns](#common-patterns)

---

## Basic Types

### Primitive Types
- `let age: number = 30`
- `let name: string = "Alice"`
- `let isActive: boolean = true`
- `let value: null = null`
- `let missing: undefined = undefined`

### Special Types
- `any` - avoid when possible
- `unknown` - safer than any, requires type checking
- `void` - no return value
- `never` - never returns (throws or infinite loop)

---

## Variables & Declarations

### Variable Declarations
- `let x: number = 10` - block-scoped, mutable
- `const y: number = 30` - block-scoped, immutable reference
- `var z: number = 50` - function-scoped, avoid

### Type Inference
- `let count = 10` - inferred as number
- `let name = "Alice"` - inferred as string
- `let active = true` - inferred as boolean

### Destructuring
- `const [first, second, ...rest] = [1, 2, 3, 4]` - array
- `const { name, age } = person` - object
- `const { name: personName, age = 0 } = person` - rename, default

---

## Functions

### Function Declarations
```typescript
function add(a: number, b: number): number { return a + b; }
const multiply = (a: number, b: number): number => a * b;
function greet(name: string, title?: string, greeting: string = "Hello"): string { }
function sum(...numbers: number[]): number { }
```

### Function Types
- `type MathOperation = (a: number, b: number) => number`
- `const add: MathOperation = (a, b) => a + b`

### Overloads
```typescript
function format(value: string): string;
function format(value: number): string;
function format(value: string | number): string { return String(value); }
```

---

## Arrays & Tuples

### Arrays
- `let numbers: number[] = [1, 2, 3]`
- `let names: Array<string> = ["Alice", "Bob"]`
- `let readonlyNumbers: ReadonlyArray<number> = [1, 2, 3]`
- Methods: `push()`, `pop()`, `map()`, `filter()`, `reduce()`, `find()`, `some()`, `every()`

### Tuples
- `let tuple: [string, number] = ["Alice", 30]`
- `let optionalTuple: [string, number?] = ["Alice"]`
- `let tupleWithRest: [string, ...number[]] = ["Alice", 1, 2, 3]`
- `let readonlyTuple: readonly [string, number] = ["Alice", 30]`

---

## Objects & Interfaces

### Object Types
- `let person: { name: string; age: number } = { name: "Alice", age: 30 }`
- `let person2: { name: string; age?: number } = { name: "Alice" }`
- `let dictionary: { [key: string]: number } = { "one": 1 }`

### Interfaces
```typescript
interface Person {
  name: string;
  age: number;
  email?: string;        // optional
  readonly id: number;   // readonly
}

interface Employee extends Person {
  employeeId: string;
  department: string;
}
```

### Type Aliases
- `type Person = { name: string; age: number }`
- `type ID = string | number`
- `type Employee = Person & { employeeId: string }`

---

## Classes

### Class Definition
```typescript
class Person {
  name: string;
  private id: number;
  protected email: string;

  constructor(name: string, age: number, id: number) {
    this.name = name;
    this.age = age;
    this.id = id;
  }

  greet(): string { return `Hello, I'm ${this.name}`; }
  get fullInfo(): string { return `${this.name} (${this.age})`; }
  set newAge(age: number) { if (age > 0) this.age = age; }
  static createDefault(): Person { return new Person("Unknown", 0, 0); }
}
```

### Inheritance
```typescript
class Employee extends Person {
  employeeId: string;
  constructor(name: string, age: number, id: number, employeeId: string) {
    super(name, age, id);
    this.employeeId = employeeId;
  }
  greet(): string { return `${super.greet()} - Employee ID: ${this.employeeId}`; }
}
```

### Abstract Classes
```typescript
abstract class Animal {
  abstract makeSound(): void;
  move(): void { console.log("Moving..."); }
}
```

### Access Modifiers
- `public` - accessible anywhere
- `private` - only within class
- `protected` - class and subclasses
- `readonly` - cannot be reassigned

---

## Enums

```typescript
enum Direction { Up, Down, Left, Right }  // numeric (0, 1, 2, 3)
enum Direction2 { Up = 1, Down, Left, Right }  // numeric (1, 2, 3, 4)
enum Status { Pending = "PENDING", Approved = "APPROVED" }  // string
const enum Size { Small, Medium, Large }  // const enum
```

---

## Unions & Intersections

### Union Types
```typescript
type ID = string | number;
function printId(id: string | number): void {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toString());
  }
}

// Discriminated unions
type Shape = { kind: "circle"; radius: number } | { kind: "rectangle"; width: number; height: number };
function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle": return Math.PI * shape.radius ** 2;
    case "rectangle": return shape.width * shape.height;
  }
}
```

### Intersection Types
- `type EmployeePerson = Person & Employee` - combines multiple types

---

## Generics

### Generic Functions
```typescript
function identity<T>(arg: T): T { return arg; }
let output = identity<string>("hello");

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

### Generic Classes
```typescript
class Box<T> {
  private value: T;
  constructor(value: T) { this.value = value; }
  getValue(): T { return this.value; }
  setValue(value: T): void { this.value = value; }
}
const numberBox = new Box<number>(42);
```

### Generic Interfaces
```typescript
interface Repository<T> {
  findById(id: string): T | null;
  findAll(): T[];
  save(entity: T): T;
}
```

---

## Type Utilities

### Utility Types
- `Partial<Person>` - all properties optional
- `Required<Person>` - all properties required
- `Readonly<Person>` - all properties readonly
- `Pick<Person, "name">` - select specific properties
- `Omit<Person, "age">` - exclude specific properties
- `Record<string, Person>` - object type with specific keys
- `Extract<"a" | "b", string>` - extract types from union
- `Exclude<string | number, string>` - exclude types from union

### Keyof & Typeof
- `type PersonKeys = keyof Person` - get keys of type
- `type PersonType = typeof person` - get type of value

### Type Assertions
- `(value as string).length` - as syntax (preferred)
- `(<string>value).length` - angle bracket syntax
- `document.getElementById("myId")!` - non-null assertion

---

## Control Flow

### If/Else & Switch
```typescript
if (condition) { } else if (anotherCondition) { } else { }
const result = condition ? valueIfTrue : valueIfFalse;

switch (value) {
  case "option1": break;
  default: break;
}
```

### Loops
- `for (let i = 0; i < 10; i++) { }`
- `for (const item of array) { }`
- `for (const key in object) { }`
- `while (condition) { }`
- `do { } while (condition)`

---

## Modules & Imports

### Export
- `export function add(a: number, b: number): number { }`
- `export const PI = 3.14`
- `export default class Calculator { }`
- `export type { Person, Employee }`
- `export { add, subtract } from "./math"`

### Import
- `import { add, subtract } from "./math"`
- `import { add as addNumbers } from "./math"`
- `import Calculator from "./calculator"`
- `import * as Math from "./math"`
- `import type { Person } from "./types"`
- `import "./styles.css"`

---

## Error Handling

```typescript
try {
  riskyOperation();
} catch (error) {
  if (error instanceof Error) {
    console.error(error.message);
  }
} finally {
  cleanup();
}

class CustomError extends Error {
  constructor(message: string, public code: number) {
    super(message);
    this.name = "CustomError";
  }
}
```

---

## Common Patterns

### Optional Chaining & Nullish Coalescing
- `person?.name` - safe property access
- `person?.address?.street` - nested safe access
- `obj?.method?.()` - safe method call
- `input ?? defaultValue` - nullish coalescing (null or undefined only)

### Type Guards
```typescript
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function process(value: string | number) {
  if (isString(value)) {
    console.log(value.toUpperCase()); // TypeScript knows it's string
  } else {
    console.log(value.toFixed(2)); // TypeScript knows it's number
  }
}
```

### Assertion Functions
```typescript
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== "string") throw new Error("Not a string");
}
```

### Mapped Types
- `type Optional<T> = { [P in keyof T]?: T[P] }`
- `type Readonly<T> = { readonly [P in keyof T]: T[P] }`

### Object Spread & Array Methods
- `const updated = { ...person, age: 31 }`
- `const merged = { ...obj1, ...obj2 }`
- `numbers.map(x => x * 2)`, `filter()`, `reduce()`, `find()`, `some()`, `every()`

### Template Literals
- `` `Hello, ${name}. You are ${age} years old.` ``
- `` `Line 1\nLine 2` ``

### Logical Operators
- `value && doSomething()` - && (first falsy or last truthy)
- `value || defaultValue` - || (first truthy or last falsy)
- `value ?? defaultValue` - ?? (first non-nullish)

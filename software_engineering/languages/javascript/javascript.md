# JavaScript Quick Reference Guide

## Table of Contents

- [Basic Types & Values](#basic-types--values)
- [Variables & Declarations](#variables--declarations)
- [Functions](#functions)
- [Arrays](#arrays)
- [Objects](#objects)
- [Classes](#classes)
- [Control Flow](#control-flow)
- [Modules & Imports](#modules--imports)
- [Error Handling](#error-handling)
- [Async & Promises](#async--promises)
- [Common Patterns](#common-patterns)
- [Built-in Objects & Methods](#built-in-objects--methods)

---

## Basic Types & Values

### Primitive Types
- `let age = 30` - number
- `let name = "Alice"` - string
- `let isActive = true` - boolean
- `let value = null` - null
- `let missing = undefined` - undefined
- `let symbol = Symbol("id")` - symbol (ES6+)
- `let bigInt = 9007199254740991n` - BigInt (ES2020+)

### Type Checking
- `typeof value` - returns type string ("number", "string", "boolean", "object", "function", "undefined", "symbol", "bigint")
- `value instanceof Constructor` - checks if value is instance of constructor
- `Array.isArray(value)` - checks if value is array
- `Number.isNaN(value)` - checks if value is NaN
- `Number.isFinite(value)` - checks if value is finite number

### Type Conversion
- `String(value)` or `value.toString()` - to string
- `Number(value)` or `parseInt(value, 10)` / `parseFloat(value)` - to number
- `Boolean(value)` - to boolean
- `BigInt(value)` - to BigInt
- `+value` - unary plus (to number)
- `!!value` - double negation (to boolean)

---

## Variables & Declarations

### Variable Declarations
- `let x = 10` - block-scoped, mutable
- `const y = 30` - block-scoped, immutable reference
- `var z = 50` - function-scoped, avoid (legacy)

### Scope
- **Block scope**: `let` and `const` are block-scoped (if, for, while, etc.)
- **Function scope**: `var` is function-scoped
- **Global scope**: variables declared without keyword (avoid)

### Hoisting
- `var` declarations are hoisted (undefined until assignment)
- `let` and `const` are hoisted but in "temporal dead zone" (error if accessed before declaration)
- Function declarations are fully hoisted

### Destructuring
- `const [first, second, ...rest] = [1, 2, 3, 4]` - array destructuring
- `const { name, age } = person` - object destructuring
- `const { name: personName, age = 0 } = person` - rename, default value
- `const { address: { street } } = person` - nested destructuring
- `const [a, , c] = [1, 2, 3]` - skip elements

---

## Functions

### Function Declarations
```javascript
function add(a, b) { return a + b; }
const multiply = (a, b) => a * b;
function greet(name, title, greeting = "Hello") { }
function sum(...numbers) { }
```

### Arrow Functions
- `const add = (a, b) => a + b` - single expression (implicit return)
- `const greet = (name) => { return `Hello ${name}`; }` - block body
- `const square = x => x * x` - single parameter (no parentheses)
- `const log = () => console.log("Hello")` - no parameters
- Arrow functions don't have their own `this`, `arguments`, or `super`

### Function Methods
- `function.bind(thisArg, ...args)` - bind `this` and arguments
- `function.call(thisArg, ...args)` - call with specific `this`
- `function.apply(thisArg, argsArray)` - call with array of arguments

### Higher-Order Functions
```javascript
function higherOrder(fn) {
  return function(...args) {
    return fn(...args);
  };
}

const numbers = [1, 2, 3];
numbers.map(x => x * 2);
numbers.filter(x => x > 1);
numbers.reduce((acc, x) => acc + x, 0);
```

### Closures
```javascript
function outer() {
  let count = 0;
  return function inner() {
    count++;
    return count;
  };
}
const counter = outer();
counter(); // 1
counter(); // 2
```

### IIFE (Immediately Invoked Function Expression)
```javascript
(function() {
  // code here
})();

(() => {
  // arrow function IIFE
})();
```

---

## Arrays

### Array Creation
- `let numbers = [1, 2, 3]`
- `let empty = []`
- `let arr = new Array(5)` - creates array with 5 empty slots
- `Array.from({ length: 5 }, (_, i) => i)` - create array from iterable
- `Array.of(1, 2, 3)` - create array from arguments

### Array Methods
- **Mutating**: `push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()`, `reverse()`
- **Non-mutating**: `map()`, `filter()`, `reduce()`, `find()`, `findIndex()`, `some()`, `every()`, `slice()`, `concat()`, `flat()`, `flatMap()`
- **Search**: `indexOf()`, `lastIndexOf()`, `includes()`
- **Iteration**: `forEach()`, `for...of`

### Array Examples
```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.map(x => x * 2); // [2, 4, 6, 8, 10]
numbers.filter(x => x > 2); // [3, 4, 5]
numbers.reduce((acc, x) => acc + x, 0); // 15
numbers.find(x => x > 3); // 4
numbers.some(x => x > 4); // true
numbers.every(x => x > 0); // true
numbers.slice(1, 3); // [2, 3]
numbers.concat([6, 7]); // [1, 2, 3, 4, 5, 6, 7]
```

### Spread Operator
- `const newArr = [...arr1, ...arr2]` - combine arrays
- `const copy = [...original]` - shallow copy
- `Math.max(...numbers)` - spread as arguments

---

## Objects

### Object Creation
```javascript
const person = { name: "Alice", age: 30 };
const person2 = new Object();
const person3 = Object.create(null);
```

### Object Methods
- `Object.keys(obj)` - array of keys
- `Object.values(obj)` - array of values
- `Object.entries(obj)` - array of [key, value] pairs
- `Object.assign(target, ...sources)` - copy properties
- `Object.freeze(obj)` - prevent modifications
- `Object.seal(obj)` - prevent adding/removing properties
- `Object.hasOwnProperty(key)` - check if property exists
- `Object.hasOwn(obj, key)` - modern alternative (ES2022+)

### Property Access
- `obj.name` - dot notation
- `obj["name"]` - bracket notation (dynamic keys)
- `obj?.name` - optional chaining (ES2020+)
- `obj?.method?.()` - optional chaining with method call

### Object Spread
- `const updated = { ...person, age: 31 }` - update property
- `const merged = { ...obj1, ...obj2 }` - merge objects
- `const copy = { ...original }` - shallow copy

### Computed Property Names
```javascript
const key = "name";
const obj = { [key]: "Alice" }; // { name: "Alice" }
```

### Property Shorthand
```javascript
const name = "Alice";
const age = 30;
const person = { name, age }; // { name: "Alice", age: 30 }
```

### Getters & Setters
```javascript
const obj = {
  _value: 0,
  get value() { return this._value; },
  set value(v) { this._value = v > 0 ? v : 0; }
};
```

---

## Classes

### Class Definition
```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }

  get fullInfo() {
    return `${this.name} (${this.age})`;
  }

  set newAge(age) {
    if (age > 0) this.age = age;
  }

  static createDefault() {
    return new Person("Unknown", 0);
  }
}
```

### Inheritance
```javascript
class Employee extends Person {
  constructor(name, age, employeeId) {
    super(name, age);
    this.employeeId = employeeId;
  }

  greet() {
    return `${super.greet()} - Employee ID: ${this.employeeId}`;
  }
}
```

### Private Fields (ES2022+)
```javascript
class Person {
  #privateField = "secret";
  #privateMethod() { }
}
```

### Static Fields & Methods
```javascript
class MathUtils {
  static PI = 3.14159;
  static add(a, b) { return a + b; }
}
```

---

## Control Flow

### If/Else & Switch
```javascript
if (condition) { } 
else if (anotherCondition) { } 
else { }

const result = condition ? valueIfTrue : valueIfFalse;

switch (value) {
  case "option1":
    break;
  case "option2":
    break;
  default:
    break;
}
```

### Loops
```javascript
for (let i = 0; i < 10; i++) { }
for (const item of array) { }
for (const key in object) { }
while (condition) { }
do { } while (condition)
```

### Loop Control
- `break` - exit loop
- `continue` - skip to next iteration
- `label:` - label for break/continue

---

## Modules & Imports

### Export (ES6 Modules)
```javascript
export function add(a, b) { return a + b; }
export const PI = 3.14;
export default class Calculator { }
export { add, subtract } from "./math";
```

### Import (ES6 Modules)
```javascript
import { add, subtract } from "./math";
import { add as addNumbers } from "./math";
import Calculator from "./calculator";
import * as Math from "./math";
import "./styles.css"; // side-effect import
```

### CommonJS (Node.js)
```javascript
// Export
module.exports = { add, subtract };
module.exports = Calculator;

// Import
const { add, subtract } = require("./math");
const Calculator = require("./calculator");
```

---

## Error Handling

```javascript
try {
  riskyOperation();
} catch (error) {
  console.error(error.message);
  console.error(error.stack);
} finally {
  cleanup();
}

class CustomError extends Error {
  constructor(message, code) {
    super(message);
    this.name = "CustomError";
    this.code = code;
  }
}

throw new CustomError("Something went wrong", 500);
```

---

## Async & Promises

### Promises
```javascript
const promise = new Promise((resolve, reject) => {
  if (success) {
    resolve(value);
  } else {
    reject(error);
  }
});

promise
  .then(value => { })
  .catch(error => { })
  .finally(() => { });
```

### Async/Await
```javascript
async function fetchData() {
  try {
    const response = await fetch(url);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error(error);
  }
}
```

### Promise Methods
- `Promise.all([promise1, promise2])` - wait for all (fails if any fails)
- `Promise.allSettled([promise1, promise2])` - wait for all (never fails)
- `Promise.race([promise1, promise2])` - first to resolve/reject
- `Promise.any([promise1, promise2])` - first to resolve (fails if all fail)
- `Promise.resolve(value)` - create resolved promise
- `Promise.reject(error)` - create rejected promise

### Fetch API
```javascript
fetch(url, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(data)
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

---

## Common Patterns

### Optional Chaining & Nullish Coalescing
- `person?.name` - safe property access
- `person?.address?.street` - nested safe access
- `obj?.method?.()` - safe method call
- `input ?? defaultValue` - nullish coalescing (null or undefined only)
- `input || defaultValue` - logical OR (any falsy value)

### Template Literals
- `` `Hello, ${name}. You are ${age} years old.` ``
- `` `Line 1\nLine 2` ``
- `` `Multiline
string` ``

### Logical Operators
- `value && doSomething()` - && (first falsy or last truthy)
- `value || defaultValue` - || (first truthy or last falsy)
- `value ?? defaultValue` - ?? (first non-nullish)

### Type Checking Patterns
```javascript
function isString(value) {
  return typeof value === "string";
}

function isArray(value) {
  return Array.isArray(value);
}

function isObject(value) {
  return value !== null && typeof value === "object" && !Array.isArray(value);
}
```

### Object Spread & Array Methods
- `const updated = { ...person, age: 31 }`
- `const merged = { ...obj1, ...obj2 }`
- `numbers.map(x => x * 2)`, `filter()`, `reduce()`, `find()`, `some()`, `every()`

### This Binding
```javascript
const obj = {
  name: "Alice",
  greet: function() {
    return `Hello, I'm ${this.name}`;
  },
  greetArrow: () => {
    return `Hello, I'm ${this.name}`; // this is not bound to obj
  }
};

const boundGreet = obj.greet.bind(obj);
const calledGreet = obj.greet.call(obj);
const appliedGreet = obj.greet.apply(obj);
```

---

## Built-in Objects & Methods

### Math
- `Math.PI`, `Math.E`
- `Math.abs()`, `Math.round()`, `Math.floor()`, `Math.ceil()`, `Math.max()`, `Math.min()`
- `Math.random()`, `Math.pow()`, `Math.sqrt()`

### Date
```javascript
const now = new Date();
const date = new Date(2024, 0, 1); // year, month (0-indexed), day
date.getFullYear();
date.getMonth();
date.getDate();
date.toISOString();
Date.now(); // timestamp
```

### String Methods
- `str.length`
- `str.charAt(index)`, `str[index]`
- `str.substring(start, end)`, `str.slice(start, end)`
- `str.split(separator)`, `str.join(separator)` (for arrays)
- `str.includes(substring)`, `str.startsWith()`, `str.endsWith()`
- `str.indexOf()`, `str.lastIndexOf()`
- `str.replace()`, `str.replaceAll()`
- `str.toLowerCase()`, `str.toUpperCase()`
- `str.trim()`, `str.trimStart()`, `str.trimEnd()`

### Number Methods
- `Number.parseInt()`, `Number.parseFloat()`
- `Number.isNaN()`, `Number.isFinite()`, `Number.isInteger()`
- `num.toFixed(digits)`, `num.toPrecision(digits)`
- `num.toString(radix)`

### JSON
- `JSON.stringify(obj)` - object to JSON string
- `JSON.parse(jsonString)` - JSON string to object

### Console
- `console.log()`, `console.error()`, `console.warn()`, `console.info()`
- `console.table()`, `console.group()`, `console.time()`, `console.assert()`


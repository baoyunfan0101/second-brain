---
tags:
  - frontend
  - dom
  - javascript
summary: JavaScript variables, data types, operators, control flow, functions, objects/arrays, this, async
use_cases:
  - web
  - scripting
---

# JavaScript

## Variables

```javascript
var a = 1;   // function scoped (avoid)
let b = 2;   // block scoped
const c = 3; // constant reference
```

Notes:
- `let` can be reassigned
- `const` cannot be reassigned
- `var` has hoisting issues

Hoisting:
```javascript
console.log(a);
var a = 10;
```
- Before execution:
```javascript
var a;        // hoisting
console.log(a); // undefined
a = 10;
```

## Data Types

Primitive types:
```javascript
number // numeric values (integers and floating-point numbers)
string // text data
boolean // true / false values
null // intentional absence of value
undefined // declared but not assigned
symbol // unique and immutable value
bigint // arbitrarily large integers
```

Reference types:
```javascript
object // collection of key-value pairs
array // ordered list of values
function // reusable block of code
```

Examples:
```javascript
// number
let a = 42;
let b = 3.14;

// string
let str1 = "hello";
let str2 = 'world';
let str3 = `hi ${str1}`;

// boolean
let isDone = true;
let isEmpty = false;

// null
let n = null;

// undefined
let u;
console.log(u); // undefined

// symbol
let s1 = Symbol("id");
let s2 = Symbol("id");
console.log(s1 === s2); // false

// bigint
let big = 1234567890123456789012345678901234567890n;
let big2 = BigInt(9007199254740991);

// object
let obj = {
  name: "Alice",
  age: 20,
  greet: function() {
    console.log("Hello");
  }
};

// array
let arr = [1, 2, 3, 4];
let mixedArr = [1, "two", true];

// function
function add(x, y) {
  return x + y;
}

let multiply = function(a, b) {
  return a * b;
};

let arrow = (n) => n * 2;
```

## Operators

```javascript
+ - * / %
=== !==   // strict comparison
== !=     // type coercion (avoid)
&& || !
```

Examples:
```javascript
0 == false     // true
0 === false    // false
[] == false    // true
```

## Control Flow

```javascript
if (x > 0) {
  ...
} else {
  ...
}

for (let i = 0; i < 5; i++) {}

while (x > 0) {}

switch (a) {
  case 1: break;
  default: break;
}
```

## Functions

```javascript
function add(a, b) {
  return a + b;
}

const add2 = (a, b) => a + b;
```

Notes:
- Functions are first-class citizens
	```javascript
	let f = function() { return 1; }; // assign to variable
	function run(fn) { return fn(); } // pass as argument
	function outer() {
	  return function() { return 2; }; // return function
	}
	```
- Support closures
	```javascript
	function outer() {
	  let x = 10; // referenced by inner, kept alive in closure
	  return function inner() {
	    return x; // uses outer scope variable
	  };
	}
	
	let fn = outer();
	console.log(fn()); // 10
	```

## Objects and Arrays

```javascript
const obj = {
  name: "Tom",
  age: 20
};

obj.name; // "Tom"

const arr = [1, 2, 3];

arr.push(4); // [1, 2, 3, 4]
arr.pop(); // [1, 2, 3]
```

## Common Array Methods

```javascript
const arr = [1, 2, 3];

arr.map(x => x * 2); // [2, 4, 6]
arr.filter(x => x > 2); // [3]
arr.reduce((a, b) => a + b, 0); // 6
arr.forEach(x => console.log(x)); // logs: 1 2 3
```

## Scope and Closure

```javascript
function outer() {
  let x = 10; // referenced by inner, kept alive in closure
  return function inner() {
	return x; // uses outer scope variable
  };
}

let fn = outer();
console.log(fn()); // 10
```

Explanation:
- Inner function remembers outer variables

## this Keyword

```javascript
// Regular function: depends on caller
const obj = {
  x: 1,
  f: function() {
    return this.x;
  }
};
obj.f(); // 1
const g = obj.f;
g(); // undefined (or window/global.x if exists)

// Arrow function: inherits outer this
const obj2 = {
  x: 1,
  f: () => this.x
};
obj2.f(); // undefined (or window/global.x if exists)

// `new`: binds to instance (this points to the new object)
function Person(name) {
  this.name = name;
}
const p = new Person("Tom"); // p = { name: "Tom" }
```

Rules:
- Regular function: depends on caller
- Arrow function: inherits outer this
- `new`: binds to instance

## Asynchronous JavaScript

Callback:
```javascript
setTimeout(() => {
  console.log("done");
}, 1000);
```

Promise:
```javascript
new Promise((resolve, reject) => {
  resolve(1);
}).then(res => console.log(res));
```

Async/Await:
```javascript
async function f() {
  // returns a Promise immediately when called
  const res = await fetch(url); // pauses here, resumes in microtask
  return res; // resolves the Promise returned by f with res
}

const p = f(); // p = Promise<Response>

p.then(res => {  
  console.log(res); // log: Response
});

const res = await f(); // res = Response
```

## DOM Manipulation

```javascript
// Selection
document.getElementById("id"); // HTMLElement | null
document.querySelector(".class"); // Element | null
document.querySelectorAll(".class"); // NodeList
document.getElementsByClassName("class"); // HTMLCollection

// Content
element.innerHTML = "<b>hello</b>"; // set HTML content
element.textContent = "hello"; // set text only

// Attributes
element.id = "newId"; // set id
element.className = "box"; // set class
element.setAttribute("data-x", "1"); // set attribute

// Style
element.style.color = "red"; // inline style
element.style.display = "none"; // hide element

// Events
element.addEventListener("click", fn); // add listener
element.removeEventListener("click", fn); // remove listener

// Node Operations
const div = document.createElement("div"); // create element
parent.appendChild(div); // append child
parent.removeChild(div); // remove child
element.remove(); // remove self
```

## Module Loading

### Static: ES Module (ESM)
```javascript
// export: expose bindings from current module
export const a = 1;

// import: load bindings from another module
import { a } from "./a.js";

// alias import
import { a as b } from "./a.js";

// import all as namespace
import * as mod from "./a.js";

// default export
export default function fn() {}

// default import
import fn from "./a.js";

// combine default + named
import fn, { a } from "./a.js";

// side-effect import
import "./setup.js";

// dynamic import
const m = await import("./a.js");

// re-export
export { a } from "./a.js";
export { default } from "./a.js";
```

### Dynamic: CommonJS (CJS) vs. ES Module (ESM)

**CommonJS: Dynamic Loading**
```javascript
if (cond) {
  const name = 'a';
  const mod = require(`./${name}.js`);
}
```
- load at runtime
- path can be dynamic

**ES Module: Dynamic Import**
```javascript
if (cond) {
  const mod = await import('./a.js');
}
```
- `import ... from` -> static (compile-time)
- `import()` -> dynamic (runtime)

## TypeScript

```typescript
// basic types
let a: number = 1; // number
let b: string = "hello"; // string
let c: boolean = true; // boolean

// array
let arr: number[] = [1, 2, 3]; // array of numbers

// tuple
let t: [number, string] = [1, "a"]; // fixed types per position

// union
let u: number | string = 1; // multiple possible types

// object type
let obj: { name: string; age: number } = {
  name: "Tom",
  age: 20
};

// function
function add(a: number, b: number): number {
  return a + b;
}

// interface
interface Person {
  name: string;
  age: number;
}

const p: Person = { name: "Tom", age: 20 };

// optional & readonly
interface User {
  id: number;
  name?: string; // optional
  readonly role: string; // readonly
}

// generics
function identity<T>(x: T): T {
  return x;
}

// class
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}

// enum
enum Color {
  Red,
  Green,
  Blue
}
```

## Object Type Checking

**JavaScript (runtime checking)**
```javascript
// typeof: basic check
typeof {}; // "object"
typeof []; // "object"
typeof null; // "object" (bug)

// array check
Array.isArray([]); // true

// null check
value === null; // true

// precise type check
Object.prototype.toString.call({}); // "[object Object]"
Object.prototype.toString.call([]); // "[object Array]"

// custom shape check
function isPerson(obj) {
  return obj &&
    typeof obj.name === "string" &&
    typeof obj.age === "number";
}
```

**TypeScript (compile-time checking)**
```typescript
// object type annotation
let obj: { name: string; age: number };

// interface
interface Person {
  name: string;
  age: number;
}

const p: Person = { name: "Tom", age: 20 };

// type alias
type User = {
  name: string;
  age: number;
};

// runtime + type guard
function isPerson(obj: any): obj is Person {
  return obj &&
    typeof obj.name === "string" &&
    typeof obj.age === "number";
}
```
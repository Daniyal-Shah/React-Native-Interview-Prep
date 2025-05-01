# **The Complete JavaScript Guide for React Native Interviews**

## **1. JavaScript Fundamentals**
### **What is JavaScript?**
- A **single-threaded**, **non-blocking**, **asynchronous**, **concurrent** scripting language.
- Runs on the **V8 engine** (Node.js/Chrome) or **JavaScriptCore** (React Native).

### **Data Types**
JavaScript has **7 primitive types** and **objects**:
```javascript
// Primitives (immutable)
typeof "Hello"      // "string"
typeof 42           // "number"
typeof true         // "boolean"
typeof undefined    // "undefined"
typeof null         // "object" (historical bug)
typeof Symbol()     // "symbol"
typeof 10n          // "bigint"

// Objects (mutable)
typeof {}           // "object"
typeof []           // "object"
typeof function(){} // "function"
```

---

## **2. Variables & Scoping**
### **Variable Declarations**
| Keyword  | Scope     | Hoisting | Reassignable | Redeclarable |
|----------|-----------|----------|--------------|--------------|
| `var`    | Function  | Yes      | Yes          | Yes          |
| `let`    | Block     | No (TDZ) | Yes          | No           |
| `const`  | Block     | No (TDZ) | No           | No           |

**Temporal Dead Zone (TDZ)**:  
Accessing `let`/`const` before declaration throws an error.

### **Scopes**
```javascript
function outer() {
  var x = 1;  // Function-scoped
  let y = 2;  // Block-scoped
  
  if (true) {
    var x = 3;  // Same variable!
    let y = 4;  // New block-scoped variable
  }
  
  console.log(x); // 3
  console.log(y); // 2
}
```

---

## **3. Functions**
### **Function Types**
```javascript
// 1. Function Declaration (hoisted)
function add(a, b) { return a + b; }

// 2. Function Expression
const multiply = function(a, b) { return a * b; };

// 3. Arrow Function (no 'this', 'arguments')
const divide = (a, b) => a / b;

// 4. IIFE (Immediately Invoked Function Expression)
(function() { console.log("IIFE!"); })();
```

### **Closures**
A function that remembers its outer variables:
```javascript
function createCounter() {
  let count = 0;
  return () => ++count; // Closure retains access to 'count'
}

const counter = createCounter();
counter(); // 1
counter(); // 2
```

---

## **4. Objects & Prototypes**
### **Object Basics**
```javascript
const obj = {
  name: "Alice",
  greet() { console.log(`Hello, ${this.name}!`); }
};

obj.greet(); // "Hello, Alice!"
```

### **Prototypal Inheritance**
```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  console.log(`Hello, ${this.name}!`);
};

const alice = new Person("Alice");
alice.greet(); // "Hello, Alice!"
```

### **Classes (ES6)**
```javascript
class Person {
  constructor(name) { this.name = name; }
  greet() { console.log(`Hello, ${this.name}!`); }
}

const bob = new Person("Bob");
bob.greet(); // "Hello, Bob!"
```

---

## **5. Arrays**
### **Common Methods**
```javascript
const nums = [1, 2, 3];

// Transformations
nums.map(x => x * 2);    // [2, 4, 6]
nums.filter(x => x > 1); // [2, 3]
nums.reduce((sum, x) => sum + x, 0); // 6

// Mutations
nums.push(4);    // [1, 2, 3, 4]
nums.pop();      // [1, 2, 3]
nums.splice(1, 1); // Removes 2: [1, 3]
```

### **Array Spread**
```javascript
const arr1 = [1, 2];
const arr2 = [...arr1, 3]; // [1, 2, 3]
```

---

## **6. Asynchronous JavaScript**
### **Callbacks**
```javascript
function fetchData(callback) {
  setTimeout(() => callback("Data"), 1000);
}

fetchData(data => console.log(data)); // "Data" after 1s
```

### **Promises**
```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("Done!"), 1000);
});

promise
  .then(result => console.log(result)) // "Done!"
  .catch(error => console.error(error));
```

### **Async/Await**
```javascript
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}
```

---

## **7. ES6+ Features**
### **Destructuring**
```javascript
const obj = { a: 1, b: 2 };
const { a, b } = obj; // a=1, b=2

const arr = [1, 2];
const [x, y] = arr; // x=1, y=2
```

### **Template Literals**
```javascript
const name = "Alice";
console.log(`Hello, ${name}!`); // "Hello, Alice!"
```

### **Default Parameters**
```javascript
function greet(name = "Guest") {
  console.log(`Hello, ${name}!`);
}
greet(); // "Hello, Guest!"
```

---

## **8. Modules**
### **CommonJS (Node.js)**
```javascript
// math.js
module.exports = { add: (a, b) => a + b };

// app.js
const { add } = require('./math');
```

### **ES Modules**
```javascript
// math.js
export const add = (a, b) => a + b;

// app.js
import { add } from './math';
```

---

## **9. Error Handling**
```javascript
try {
  throw new Error("Oops!");
} catch (error) {
  console.error(error.message); // "Oops!"
} finally {
  console.log("Always runs");
}
```

---

## **10. TypeScript for React Native**
### **Key Features**
- **Static typing** for JavaScript
- **Interfaces**, **Generics**, **Enums**
- **Compiler checks** for errors

### **React Native Example**
```typescript
interface User {
  id: number;
  name: string;
}

const user: User = { id: 1, name: "Alice" };

function greet(user: User): string {
  return `Hello, ${user.name}!`;
}
```

---

## **Interview Questions & Answers**
### **Q: What is the event loop?**
**A**: JavaScript uses a **single-threaded event loop** to handle asynchronous operations. It processes the call stack, then checks the callback queue for pending tasks.

### **Q: Explain `this` keyword.**
**A**: `this` refers to the execution context:
- **Global**: `window` (browser) / `global` (Node.js)
- **Function**: Depends on how it's called
- **Arrow function**: Inherits from parent scope

### **Q: What are promises?**
**A**: Promises represent the eventual completion (or failure) of an async operation. They can be in **pending**, **fulfilled**, or **rejected** states.

---

## **Final Tips**
1. **Master closures** and `this` binding.
2. **Practice array methods** (`map`, `filter`, `reduce`).
3. **Understand async/await** and promises.
4. **Learn TypeScript** for modern React Native roles.

This guide covers **everything you need** for JavaScript/TypeScript questions in React Native interviews! 🚀

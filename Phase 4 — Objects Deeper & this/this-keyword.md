# 18. The `this` Keyword in JavaScript

## 1. Core Rule of `this`
Unlike variables (which are resolved lexically by where code is written), **`this` is determined by HOW and WHERE a function is called (invoked) at runtime**.

> **Golden Rule:** Check what is on the **left side of the dot** at the time of invocation. That object is `this`.

```
                    How was the function invoked?
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
  Direct function call      Method call           Arrow function
    show()                  user.show()              () => {}
  (Global or undefined)   (The 'user' object)   (Inherited from lexical scope)
```

---

## 2. `this` in the Global Scope (Browser vs. Node.js)

At the top level (outside any function):

### A. Browser Environment
`this` refers directly to the global `window` object.
```javascript
console.log(this === window); // true (in browser)
```

### B. Node.js Environment
* Inside a **file/module**: `this` points to `module.exports` (an empty object `{}` initially).
* Inside the **Node.js REPL**: `this` points to the `global` object.

```javascript
// Inside a Node.js script (.js file)
console.log(this); // {}
console.log(this === module.exports); // true
```

**Output:**
```text
{}
true
```

---

## 3. `this` in Regular Functions (Non-Strict vs. Strict Mode)

### A. Non-Strict Mode (Default)
When a regular function is called directly without any owner object, `this` defaults to the **Global Object** (`window` in browsers, `global` in Node.js).

```javascript
function showGlobal() {
  console.log("Is global object?:", this === globalThis);
}

showGlobal();
```

**Output:**
```text
Is global object?: true
```

---

### B. Strict Mode (`"use strict"`)
In strict mode, JavaScript disables the default global binding. Unbound functions have `this` set strictly to **`undefined`** to prevent accidental global mutations.

```javascript
"use strict";

function showStrict() {
  console.log("Strict this value:", this);
}

showStrict();
```

**Output:**
```text
Strict this value: undefined
```

---

## 4. `this` Inside Object Methods

When a function is invoked as a method of an object (`obj.method()`), `this` refers to the **object before the dot**.

```javascript
const account = {
  holder: "Aryan",
  balance: 25000,
  printSummary() {
    console.log(`${this.holder} has a balance of ₹${this.balance}`);
  }
};

account.printSummary();
```

**Output:**
```text
Aryan has a balance of ₹25000
```

---

### ⚠️ The "Losing `this`" Trap (Method Extraction)
Extracting a method into a standalone variable breaks its reference because it gets called as a regular function.

```javascript
const person = {
  name: "Rohan",
  greet() {
    console.log("Hello, I am", this ? this.name : undefined);
  }
};

// Method invocation:
person.greet(); 

// Reference assignment (Loses its object context):
const standaloneGreet = person.greet;
standaloneGreet(); // Called without an object before the dot!
```

**Output:**
```text
Hello, I am Rohan
Hello, I am undefined
```

---

## 5. `this` in Arrow Functions (Lexical `this`)

Arrow functions do **NOT** have their own `this` binding. Instead, they capture the `this` value of the enclosing lexical scope at the time they are created.

### A. Arrow Function Inside Object Literal (Anti-Pattern)
```javascript
const user = {
  name: "Pooja",
  // ❌ Arrow function captures global `this`, NOT the `user` object
  sayHi: () => {
    console.log("Arrow this.name:", this.name);
  }
};

user.sayHi();
```

**Output:**
```text
Arrow this.name: undefined
```

---

### B. Arrow Function Inside a Method / Callback (Super Power)
Arrow functions solve the classic callback issue because they do not overwrite `this`.

```javascript
const timer = {
  seconds: 0,
  start() {
    // Arrow function retains `this` from start() -> `timer` object
    setTimeout(() => {
      this.seconds += 1;
      console.log("Elapsed seconds:", this.seconds);
    }, 50);
  }
};

timer.start();
```

**Output:**
```text
Elapsed seconds: 1
```

---

## 6. `this` in DOM Event Handlers

In standard browser event listeners, `this` points directly to the **DOM element that received the event** (the element attached to `addEventListener`).

```javascript
// (Browser Context)
const button = document.querySelector("#submit-btn");

// 1. Regular Function -> `this` is the button element
button.addEventListener("click", function() {
  console.log("Clicked element:", this); // <button id="submit-btn">...</button>
  this.classList.add("active");
});

// 2. Arrow Function -> `this` is NOT the button (it is `window`)
button.addEventListener("click", () => {
  console.log("Arrow in event handler:", this); // Window
});
```

---

## 7. Master Decision Flowchart for `this`

Ask these 4 questions in order:

```
1. Was the function called with `new`?
   └── YES ──► `this` is the newly created instance object.

2. Was the function called with `call`, `apply`, or `bind`?
   └── YES ──► `this` is the explicitly passed object argument.

3. Was the function called as a method (`obj.method()`)?
   └── YES ──► `this` is the object before the dot (`obj`).

4. Is it an arrow function?
   └── YES ──► `this` is inherited from the outer lexical scope.

5. Otherwise (Standard function invocation `fn()`):
   └── Strict mode: `this` is `undefined`
   └── Non-strict mode: `this` is `globalThis` (`window` / `global`)
```

---

## 8. Summary Comparison Table

| Context | Non-Strict Mode | Strict Mode (`"use strict"`) |
| :--- | :--- | :--- |
| **Global Scope (Browser)** | `window` | `window` |
| **Global Scope (Node file)**| `module.exports` (`{}`) | `module.exports` (`{}`) |
| **Plain Function Call** | `globalThis` (`window`/`global`) | `undefined` |
| **Method Call (`obj.fn()`)** | Calling object (`obj`) | Calling object (`obj`) |
| **Arrow Function** | Lexical parent scope | Lexical parent scope |
| **DOM Event Listener** | The target DOM element | The target DOM element |
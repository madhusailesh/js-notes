# 15. Hoisting in JavaScript

## 1. Overview
**Hoisting** is JavaScript's default behavior of allocating memory for variable and function declarations during the **Creation Phase** of the Execution Context before any code is actually executed.

It often looks like variables and functions are "physically moved to the top of the code," but in reality, **the code stays where it is**; the JavaScript engine simply has already registered them in memory.

```
       What you write                   How JS handles it in Memory
  ------------------------             -----------------------------
  console.log(a);                      // Memory allocated during Creation Phase:
  var a = 10;                          // a = undefined
                                       // Execution Phase runs:
                                       // console.log(undefined) -> a = 10
```

---

## 2. `var` Hoisting (Initialized as `undefined`)

When variables declared with `var` are hoisted, they are allocated memory and **immediately initialized with the value `undefined`**.

```javascript
console.log("Value before declaration:", myVar);

var myVar = 100;

console.log("Value after declaration:", myVar);
```

**Output:**
```text
Value before declaration: undefined
Value after declaration: 100
```

> **Why?** During the Creation Phase, the engine reserves memory for `myVar` and sets its value to `undefined`. During the Execution Phase, when it hits `myVar = 100`, the value in memory is updated.

---

## 3. `let` and `const` Hoisting & The Temporal Dead Zone (TDZ)

A common misconception is that `let` and `const` are not hoisted. **They ARE hoisted**, but unlike `var`, they are **not initialized** with a default value.

* **Temporal Dead Zone (TDZ):** The period of time between the start of the block scope and the line where the variable is declared and initialized.
* Accessing a `let` or `const` variable inside the TDZ throws a **`ReferenceError`**.

```
{
  // <--- Start of Block Scope
  // | 
  // |   TEMPORAL DEAD ZONE (TDZ) for `x`
  // |   (Accessing x here throws ReferenceError)
  // |
  console.log(x); // ❌ ReferenceError: Cannot access 'x' before initialization
  
  let x = 50;     // <--- Declaration & Initialization (TDZ ends here)
  console.log(x); // ✅ Allowed: 50
}
```

### Code Example:
```javascript
try {
  console.log(myLet);
} catch (error) {
  console.log("Error caught:", error.name);
  console.log("Error message:", error.message);
}

let myLet = 200;
console.log("After initialization:", myLet);
```

**Output:**
```text
Error caught: ReferenceError
Error message: Cannot access 'myLet' before initialization
After initialization: 200
```

---

## 4. Function Declarations vs. Function Expressions

How functions behave with hoisting depends entirely on **how they are defined**.

```
                           Function Hoisting
                                   │
         ┌─────────────────────────┴─────────────────────────┐
         ▼                                                   ▼
Function Declarations                               Function Expressions
(e.g., function greet() {})                    (e.g., var/const greet = () => {})
Hoisted completely with body!                  Hoisted like a normal variable!
Can be called before definition.               Cannot be called before definition.
```

---

### A. Function Declarations (Fully Hoisted)
The entire function definition (name + body) is stored in memory during the Creation Phase. You can safely call it before its declaration.

```javascript
// Calling function before its definition in code
console.log(greetUser("Aarav"));

function greetUser(name) {
  return `Hello, ${name}!`;
}
```

**Output:**
```text
Hello, Aarav!
```

---

### B. Function Expressions with `var`
The variable is hoisted and initialized to `undefined`. Calling it before assignment results in a **`TypeError: ... is not a function`** (because `undefined()` is invalid).

```javascript
try {
  sayHello();
} catch (error) {
  console.log("Error type:", error.name);
  console.log("Error message:", error.message);
}

var sayHello = function() {
  console.log("Hello from expression!");
};
```

**Output:**
```text
Error type: TypeError
Error message: sayHello is not a function
```

---

### C. Function Expressions & Arrow Functions with `let` / `const`
The variable is hoisted into the **TDZ**. Calling it before assignment results in a **`ReferenceError`**.

```javascript
try {
  calcSquare(5);
} catch (error) {
  console.log("Error type:", error.name);
  console.log("Error message:", error.message);
}

const calcSquare = (n) => n * n;
```

**Output:**
```text
Error type: ReferenceError
Error message: Cannot access 'calcSquare' before initialization
```

---

## 5. Hoisting Precedence: Functions vs. Variables

When a function and a `var` share the exact same name:
* **Function declarations are hoisted before variable declarations.**
* Variable declarations without assignment will not overwrite a hoisted function.

```javascript
console.log(typeof duplicateItem);

var duplicateItem = 10;
function duplicateItem() {
  return "I am a function";
}

console.log(typeof duplicateItem);
```

**Output:**
```text
function
number
```

**Under the hood:**
1. Creation Phase: `duplicateItem` is stored as a function. The `var duplicateItem` declaration is ignored because the name already exists.
2. Execution Phase: `typeof duplicateItem` prints `"function"`.
3. Then `duplicateItem = 10` executes, reassigning it to a number. Second check prints `"number"`.

---

## 6. Summary Comparison Table

| Declaration Type | Hoisted? | Initial Value in Creation Phase | Accessing Before Line |
| :--- | :--- | :--- | :--- |
| **`var`** | ✅ Yes | `undefined` | Returns `undefined` |
| **`let` / `const`** | ✅ Yes | `<uninitialized>` (in TDZ) | Throws `ReferenceError` |
| **Function Declaration** | ✅ Yes | Entire function definition | Runs function normally |
| **Function Expression (`var`)** | ✅ Yes | `undefined` | Throws `TypeError` |
| **Function Expression (`const`/`let`)** | ✅ Yes | `<uninitialized>` (in TDZ) | Throws `ReferenceError` |

---

## 7. Best Practices to Prevent Hoisting Bugs

1. **Always use `const` and `let`:** Avoid `var` completely in modern JavaScript.
2. **Declare variables at the top of their scope:** Avoid accessing variables before their definition.
3. **Prefer arrow functions / expressions assigned to `const`:** Enforces predictable top-to-bottom flow.
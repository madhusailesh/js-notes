# 16. Scope & Scope Chain in JavaScript

## 1. Overview
**Scope** in JavaScript determines the **accessibility (visibility)** of variables, functions, and objects in different parts of your code during runtime.

If a variable is not in the current scope, it is inaccessible, and attempting to access it results in a `ReferenceError`.

```
+-------------------------------------------------------------+
|                        GLOBAL SCOPE                         |
|   const globalVar = "Accessible everywhere";                |
|                                                             |
|   +-----------------------------------------------------+   |
|   |                   FUNCTION SCOPE                    |   |
|   |   function example() {                              |   |
|   |     const funcVar = "Only inside function";         |   |
|   |                                                     |   |
|   |     +-------------------------------------------+   |   |
|   |     |                BLOCK SCOPE                |   |   |
|   |     |   if (true) {                             |   |   |
|   |     |     const blockVar = "Only inside block"; |   |   |
|   |     |   }                                       |   |   |
|   |     +-------------------------------------------+   |   |
|   |   }                                                 |   |   |
|   +-----------------------------------------------------+   |
+-------------------------------------------------------------+
```

---

## 2. The 3 Main Types of Scope

### A. Global Scope
* Any variable declared outside of all functions or blocks belongs to the **Global Scope**.
* Accessible from anywhere in the file/application.

```javascript
const appName = "NotesApp";

function printAppName() {
  console.log("Inside function:", appName);
}

printAppName();
console.log("In global space:", appName);
```

**Output:**
```text
Inside function: NotesApp
In global space: NotesApp
```

---

### B. Function Scope (Local Scope)
* Variables declared inside a function are accessible **only within that function**.
* Created when the function is invoked and destroyed when the function finishes.
* Variables declared with `var`, `let`, and `const` all respect function boundaries.

```javascript
function greet() {
  var secret = "12345";
  let message = "Hello!";
  console.log("Inside function:", secret, message);
}

greet();

try {
  console.log(secret);
} catch (err) {
  console.log("Accessing outside function:", err.name);
}
```

**Output:**
```text
Inside function: 12345 Hello!
Accessing outside function: ReferenceError
```

---

### C. Block Scope (`let` & `const` vs. `var`)
A **block** is any code bounded by curly braces `{ ... }` (such as `if`, `for`, `while`, `switch`, or standalone `{}`).

* **`let` and `const` are Block-Scoped:** They only live inside the `{}` where they are defined.
* **`var` is NOT Block-Scoped:** `var` ignores `{}` blocks and leaks into the enclosing function or global scope!

```javascript
if (true) {
  var varLeaked = "I can escape the block!";
  let letProtected = "I stay inside the block!";
  const constProtected = "I also stay inside!";
}

console.log("var value:", varLeaked); // Leaked!

try {
  console.log(letProtected);
} catch (err) {
  console.log("let access error:", err.name);
}
```

**Output:**
```text
var value: I can escape the block!
let access error: ReferenceError
```

---

## 3. What Does "Lexical Scope" Really Mean?

* **Lexical** means **"where it is physically written in the source code"** (its compile-time position).
* **Lexical Scope** means an inner function has access to variables defined in its outer (parent) enclosing scope simply based on **where the function is authored**, regardless of where it is eventually called.

```javascript
function outer() {
  const outerVar = "From Outer";

  function inner() {
    // Looks lexically outward to parent `outer()`
    console.log("Inner accessed:", outerVar);
  }

  inner();
}

outer();
```

**Output:**
```text
Inner accessed: From Outer
```

---

## 4. The Scope Chain (How JavaScript Looks Up Variables)

When JavaScript tries to read a variable, it searches **bottom-to-top**:
1. It first checks the **current local scope**.
2. If not found, it steps outward to the **parent's lexical scope**.
3. It continues outward through nested parent scopes until it hits the **Global Scope**.
4. If the variable is not found in the Global Scope, it throws a **`ReferenceError`**.

```
+------------------------------------+
|  Global Scope (a = 1)              |  ▲  (4. Not found? -> ReferenceError)
+------------------------------------+  │
|  Parent Scope (b = 2)              |  │  (3. Steps up to parent)
+------------------------------------+  │
|  Current Local Scope (c = 3)       |  │  (1. Checks local first)
+------------------------------------+  │
                                        Scope Chain Lookup Direction
```

### Scope Chain Lookup Example:
```javascript
const globalVal = "GLOBAL";

function parent() {
  const parentVal = "PARENT";

  function child() {
    const childVal = "CHILD";

    // childVal is local
    // parentVal is found 1 level up in parent()
    // globalVal is found 2 levels up in global
    console.log(childVal, "->", parentVal, "->", globalVal);
  }

  child();
}

parent();
```

**Output:**
```text
CHILD -> PARENT -> GLOBAL
```

---

## 5. Variable Shadowing

**Shadowing** occurs when a variable declared in an inner scope has the exact same name as a variable in an outer scope. The inner variable "shadows" (hides) the outer variable inside that inner block.

```javascript
const theme = "light"; // Outer variable

function updateTheme() {
  const theme = "dark"; // Inner variable shadows outer
  console.log("Inside function (Shadowed):", theme);
}

updateTheme();
console.log("In global scope (Original):", theme);
```

**Output:**
```text
Inside function (Shadowed): dark
In global scope (Original): light
```

### ⚠️ Illegal Shadowing:
You cannot shadow a `let` or `const` variable using `var` inside the same block:

```javascript
let num = 10;

{
  // ❌ SyntaxError: Identifier 'num' has already been declared
  // var num = 20; 
}
```

---

## 6. Summary Comparison Table

| Scope Type | Created By | Respected By `let`/`const`? | Respected By `var`? |
| :--- | :--- | :--- | :--- |
| **Global Scope** | Default runtime file environment | ✅ Yes | ✅ Yes |
| **Function Scope** | `function() { ... }` | ✅ Yes | ✅ Yes |
| **Block Scope** | `if`, `for`, `{ ... }` | ✅ **Yes** | ❌ **No (leaks out)** |
| **Lexical Scope** | Determined by author-time position | ✅ Yes | ✅ Yes |

---

## 7. Key Interview Takeaways

1. **One-Way Lookup:** The scope chain lookup is strictly **inside-out**. Outer functions cannot look down into inner functions to access their variables.
2. **`var` in Loops Trap:** Because `var` is not block-scoped, using `var i` in a `for` loop leaves `i` accessible outside the loop (and causes famous closure timing bugs). Always use `let` in loops.
# 14. The Call Stack in JavaScript

## 1. Overview
The **Call Stack** is a fundamental data structure used by the JavaScript engine to keep track of **where the program currently is in its execution** and **which functions are currently being called**.

* **Mechanism:** It operates on a **LIFO (Last In, First Out)** principle: the last function pushed onto the stack is the first one to be completed and popped off.
* **Single Threaded:** JavaScript has only **one Call Stack**, meaning it can only execute one function at a time.

```
       +-------------------------+
       |   Function C (Top)      |  <-- Currently Executing
       +-------------------------+
       |   Function B            |
       +-------------------------+
       |   Function A            |
       +-------------------------+
       | Global Execution Context|  <-- Base of Stack
       +-------------------------+
```

---

## 2. How the Call Stack Tracks Function Calls

When your script runs:
1. The engine creates the **Global Execution Context (GEC)** and pushes it onto the bottom of the stack.
2. Every time a function is **invoked**, the engine creates a new **Function Execution Context (FEC)** and pushes it on top of the stack.
3. The engine executes the function on top of the stack.
4. When that function hits a `return` statement or reaches the end of its block, its context is **popped off** the stack, and execution resumes where it left off in the calling context below it.

---

## 3. Visualizing the Call Stack Step-by-Step

Let's trace a nested function sequence:

```javascript
function first() {
  console.log("Inside first()");
  second();
  console.log("Exiting first()");
}

function second() {
  console.log("Inside second()");
  third();
  console.log("Exiting second()");
}

function third() {
  console.log("Inside third()");
}

first();
```

**Output:**
```text
Inside first()
Inside second()
Inside third()
Exiting second()
Exiting first()
```

---

### Step-by-Step Stack Lifecycle:

```
Step 1: Program starts          Step 2: first() called          Step 3: second() called
|                         |     |                         |     |                         |
|                         |     |                         |     |  second()               |
|                         |     |  first()                |     |  first()                |
|  Global Execution (GEC) |     |  Global Execution (GEC) |     |  Global Execution (GEC) |
+-------------------------+     +-------------------------+     +-------------------------+

Step 4: third() called          Step 5: third() finishes        Step 6: second() finishes
|  third()                |     |                         |     |                         |
|  second()               |     |  second()               |     |                         |
|  first()                |     |  first()                |     |  first()                |
|  Global Execution (GEC) |     |  Global Execution (GEC) |     |  Global Execution (GEC) |
+-------------------------+     +-------------------------+     +-------------------------+

Step 7: first() finishes        Step 8: Program terminates
|                         |     |                         |
|                         |     |                         |
|                         |     |                         |
|  Global Execution (GEC) |     |  (Empty)                |
+-------------------------+     +-------------------------+
```

---

## 4. Stack Overflow (Exceeding Maximum Call Stack Size)

A **Stack Overflow** occurs when the number of function calls exceeds the memory capacity of the Call Stack. This almost always happens due to **unbounded recursion** (a recursive function without a base condition or with an unreachable base condition).

### A. Reproducing a Stack Overflow:
```javascript
function recurseForever() {
  recurseForever(); // No exit / base condition
}

try {
  recurseForever();
} catch (error) {
  console.log("Caught Error:", error.name);
  console.log("Error Message:", error.message);
}
```

**Output:**
```text
Caught Error: RangeError
Error Message: Maximum call stack size exceeded
```

---

### B. The Fix: Proper Base Case
Always provide a termination condition to allow functions to return and pop off the stack:

```javascript
function countDown(n) {
  // Base case: Stop when n <= 0
  if (n <= 0) {
    console.log("Done!");
    return;
  }

  console.log("Count:", n);
  countDown(n - 1);
}

countDown(3);
```

**Output:**
```text
Count: 3
Count: 2
Count: 1
Done!
```

---

## 5. Viewing the Call Stack in Developer Tools

You can inspect the Call Stack in real-time in any browser:
1. Open Chrome DevTools (`F12` or `Ctrl + Shift + I`).
2. Navigate to the **Sources** (or **Debugger**) tab.
3. Set a **Breakpoint** on a line inside a nested function.
4. Refresh or trigger the function: look at the **Call Stack panel** on the right side to inspect every active execution frame and its local variables!

Alternatively, print the stack trace programmatically with `console.trace()`:

```javascript
function deepFunction() {
  console.trace("Current Stack Trace:");
}

function outerFunction() {
  deepFunction();
}

outerFunction();
```

**Output:**
```text
Current Stack Trace:
    at deepFunction (index.js:2:11)
    at outerFunction (index.js:6:3)
    at index.js:9:1
```

---

## 6. Summary Cheat Sheet

| Term | Definition |
| :--- | :--- |
| **Call Stack** | LIFO data structure storing active Execution Contexts. |
| **Push** | Adding a Function Execution Context when a function is invoked. |
| **Pop** | Removing a Function Execution Context when a function returns. |
| **Stack Overflow** | `RangeError: Maximum call stack size exceeded` caused by runaway recursion. |
| **`console.trace()`** | Logs the active call stack frames at that exact line of code. |
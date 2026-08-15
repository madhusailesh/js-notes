# 13. Execution Context in JavaScript

## 1. Overview
An **Execution Context (EC)** is the conceptual "environment" or "container" created by the JavaScript engine to evaluate and execute code. 

Everything in JavaScript happens inside an Execution Context.

```
+-------------------------------------------------------------+
|                      Execution Context                      |
|                                                             |
|   +---------------------------------+  +-----------------+  |
|   |    Memory (Variable Environment)|  | Code (Thread of |  |
|   | - Variables (keys & values)     |  |   Execution)    |  |
|   | - Function declarations         |  | - Line-by-line  |  |
|   | - Scope Chain references        |  |   execution     |  |
|   | - 'this' binding                |  |                 |  |
|   +---------------------------------+  +-----------------+  |
+-------------------------------------------------------------+
```

---

## 2. Types of Execution Contexts

### A. Global Execution Context (GEC)
* Created by default when the JS file first starts running.
* **Only one** GEC exists per JavaScript program.
* Creates two global entities:
  * **Global Object** (`window` in browsers, `global` in Node.js).
  * **`this` variable** (points to the Global Object in non-strict mode).

### B. Function Execution Context (FEC)
* Created **every time a function is invoked (called)**.
* Each function call gets its own independent execution context containing its arguments, local variables, and inner functions.
* Once the function finishes returning, its FEC is destroyed and popped off the Call Stack.

### C. `eval()` Execution Context
* Created inside code executed within an `eval()` function (rarely used and discouraged in modern JS).

---

## 3. The Two Phases of Execution Context

Every Execution Context runs in **two distinct phases**:

```
                  ┌────────────────────────────────────────┐
                  │       Execution Context Lifecycle      │
                  └───────────────────┬────────────────────┘
                                      │
               ┌──────────────────────┴──────────────────────┐
               ▼                                             ▼
     Phase 1: Creation Phase                      Phase 2: Execution Phase
  (Memory Allocation / Hoisting)                  (Code Runs Line-by-Line)
  - Allocates memory for variables                - Executes expressions & assignments
  - `var` initialized as `undefined`              - Values assigned to variables
  - `let`/`const` uninitialized (TDZ)             - Functions actually invoked
  - Function declarations stored in full
  - Sets up Scope Chain & `this`
```

---

## 4. Step-by-Step Code Execution Trace

Let's trace what happens under the hood with this code:

```javascript
var a = 10;
var b = 20;

function add(num1, num2) {
  var sum = num1 + num2;
  return sum;
}

var result = add(a, b);
console.log("Result:", result);
```

**Output:**
```text
Result: 30
```

---

### Step 1: Creation Phase of GEC (Before any code runs)
The engine scans the code and allocates memory:

| Memory Component | Initial Value in Memory |
| :--- | :--- |
| `a` | `undefined` |
| `b` | `undefined` |
| `add` | `function add(num1, num2) { ... }` (Entire function body stored) |
| `result` | `undefined` |
| `window` / `global` | Created |
| `this` | Points to Global Object |

---

### Step 2: Execution Phase of GEC (Line-by-line)
The engine executes the code top-to-bottom:

1. `var a = 10;` $\rightarrow$ Memory value of `a` updates from `undefined` to `10`.
2. `var b = 20;` $\rightarrow$ Memory value of `b` updates from `undefined` to `20`.
3. Function `add` declaration is skipped (already stored in memory).
4. `var result = add(a, b);` $\rightarrow$ **A new Function Execution Context (FEC) is created for `add(10, 20)`!**

---

### Step 3: Inside the `add()` Function Execution Context

#### A. FEC Creation Phase (for `add`):
* Parameters allocated in memory: `num1: 10`, `num2: 20`
* `sum: undefined`
* `arguments` object created: `[10, 20]`
* `this` is assigned.

#### B. FEC Execution Phase (for `add`):
* `var sum = num1 + num2;` $\rightarrow$ `sum` becomes `30`.
* `return sum;` $\rightarrow$ Returns `30` back to the GEC.
* **The `add` FEC is completely destroyed from memory.**

---

### Step 4: Back to GEC Execution
* `result` gets assigned the returned value `30`.
* `console.log("Result:", result);` prints `Result: 30`.
* Program finishes $\rightarrow$ GEC is destroyed.

---

## 5. Summary Cheat Sheet

| Feature | Creation Phase | Execution Phase |
| :--- | :--- | :--- |
| **Variables (`var`)** | Allocated memory, initialized as `undefined` | Assigned actual values |
| **Variables (`let`/`const`)** | Allocated memory, placed in **TDZ** (uninitialized) | Assigned actual values |
| **Function Declarations** | Entire function body copied into memory | Skipped until called |
| **Function Invocations** | Creates a brand new FEC | Executes function body line-by-line |
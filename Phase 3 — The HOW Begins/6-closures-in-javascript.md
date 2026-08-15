# 17. Closures in JavaScript

## 1. What is a Closure? (The Real Definition)

A **Closure** is created when an inner function is defined inside an outer function and **retains access to the outer function's variables and lexical environment**, even **after the outer function has finished executing and has been popped off the Call Stack**.

In simple terms:
> **A function bundled together with references to its surrounding lexical environment.**

```
+-------------------------------------------------------------+
|                     Outer Function Scope                    |
|   let outerVar = "I stay in memory!";                       |
|                                                             |
|   +-----------------------------------------------------+   |
|   |                 Inner Function (Closure)            |   |
|   |   function inner() {                                |   |
|   |     // Closes over & remembers `outerVar`           |   |
|   |     console.log(outerVar);                          |   |
|   |   }                                                 |   |
|   +-----------------------------------------------------+   |
+-------------------------------------------------------------+
```

---

## 2. Basic Closure Example

```javascript
function outerFunction() {
  let counter = 0;

  function innerFunction() {
    counter++;
    return counter;
  }

  return innerFunction; // Returning function definition, NOT calling it
}

// outerFunction executes and is popped off the Call Stack
const increment = outerFunction();

console.log(increment());
console.log(increment());
console.log(increment());
```

**Output:**
```text
1
2
3
```

---

## 3. Step-by-Step Under the Hood Trace

Why didn't `counter` get garbage collected when `outerFunction()` finished?

```
Step 1: outerFunction() is invoked
  - A new Function Execution Context (FEC) is created.
  - Variable `counter = 0` is allocated in memory.
  - `innerFunction` is created with a hidden internal property [[Scopes]] 
    pointing to outerFunction's lexical environment.

Step 2: outerFunction() returns `innerFunction`
  - outerFunction's execution context is POPPED off the Call Stack.
  - BUT: The engine detects that `innerFunction` holds an active reference 
    to `counter`.
  - Therefore, `counter` is NOT garbage collected. It moves to a special 
    "Closure Memory" heap allocated for that function instance.

Step 3: increment() is called
  - A new FEC is created for `innerFunction`.
  - It looks for `counter` in its local scope -> not found.
  - It checks its [[Scopes]] closure reference -> finds `counter`.
  - Updates `counter` in place.
```

---

## 4. Practical Real-World Use Cases

### A. Data Privacy / Encapsulation (Private Variables)
JavaScript didn't historically have private variables. Closures allow simulating private properties that cannot be modified directly from the outside.

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable

  return {
    deposit(amount) {
      if (amount > 0) balance += amount;
      return `Balance: ₹${balance}`;
    },
    withdraw(amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
        return `Balance: ₹${balance}`;
      }
      return "Insufficient funds or invalid amount!";
    },
    getBalance() {
      return `Current Balance: ₹${balance}`;
    }
  };
}

const myAccount = createBankAccount(1000);

console.log(myAccount.deposit(500));
console.log(myAccount.withdraw(200));
console.log(myAccount.getBalance());

// Attempting direct access:
console.log("Direct balance access:", myAccount.balance); // Cannot touch internal variable!
```

**Output:**
```text
Balance: ₹1500
Balance: ₹1300
Current Balance: ₹1300
Direct balance access: undefined
```

---

### B. Function Factories
Create customized functions with pre-configured settings.

```javascript
function createMultiplier(multiplier) {
  return function (number) {
    return number * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log("Double 5:", double(5));
console.log("Triple 5:", triple(5));
```

**Output:**
```text
Double 5: 10
Triple 5: 15
```

---

### C. Function Currying
Transforming a function with multiple arguments into a sequence of functions that take one argument at a time.

```javascript
function sendEmail(from) {
  return function (to) {
    return function (subject) {
      return `From: ${from} | To: ${to} \vert{} Subject: "${subject}"`;
    };
  };
}

// Reusable partially configured email sender:
const supportEmail = sendEmail("support@company.com");
const emailToCustomer = supportEmail("customer101@gmail.com");

console.log(emailToCustomer("Ticket #8942 Resolved"));
```

**Output:**
```text
From: support@company.com | To: customer101@gmail.com | Subject: "Ticket #8942 Resolved"
```

---

## 5. The Famous Interview Trap: Closure in a `for` Loop

### The Buggy Code with `var`:
```javascript
for (var i = 1; i <= 3; i++) {
  setTimeout(function () {
    console.log("var index:", i);
  }, 100);
}
```

**Output:**
```text
var index: 4
var index: 4
var index: 4
```

### Why Did This Happen?
* `var` is **function/globally scoped**, not block scoped.
* There is only **one shared `i` variable** across the entire loop.
* By the time `setTimeout` callbacks execute (after 100ms), the loop has already completed and `i` has become `4`. All 3 callbacks share that same reference.

---

### Solution 1: Use `let` (Block Scope - Recommended)
`let` creates a **brand new lexical scope binding for `i` on every single iteration**.

```javascript
for (let i = 1; i <= 3; i++) {
  setTimeout(function () {
    console.log("let index:", i);
  }, 100);
}
```

**Output:**
```text
let index: 1
let index: 2
let index: 3
```

---

### Solution 2: Use an IIFE (Immediately Invoked Function Expression)
Before `let` existed in ES6, developers used IIFEs to create a new function scope on each iteration:

```javascript
for (var i = 1; i <= 3; i++) {
  (function (capturedIndex) {
    setTimeout(function () {
      console.log("IIFE index:", capturedIndex);
    }, 100);
  })(i);
}
```

**Output:**
```text
IIFE index: 1
IIFE index: 2
IIFE index: 3
```

---

## 6. Memory Considerations: Can Closures Cause Memory Leaks?

Because closures hold onto references of parent scope variables, those variables **cannot be garbage collected** as long as the inner function is still referenced.

* **Tip:** If a closure references a large object or DOM element that is no longer needed, set the outer reference or function variable to `null` to allow garbage collection.

```javascript
let heavyRunner = (function () {
  let largeData = new Array(1000000).fill("data");
  return function () {
    return largeData.length;
  };
})();

console.log("Data size:", heavyRunner());

// Clear reference when done to release memory
heavyRunner = null;
```

**Output:**
```text
Data size: 1000000
```

---

## 7. Summary Cheat Sheet

| Aspect | Key Concept |
| :--- | :--- |
| **Definition** | Function + its lexical environment bundled together. |
| **Why it Works** | Engine preserves variables in heap memory if an active closure references them. |
| **Primary Use Cases** | Data privacy, function factories, currying, event handlers. |
| **Classic Loop Bug** | `var` shares one reference across iterations $\rightarrow$ solve with `let` or IIFE. |
| **Memory Warning** | Unused closures can prevent garbage collection if references stay alive. |
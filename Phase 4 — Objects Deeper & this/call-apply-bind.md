# 19. Explicit Binding: `call()`, `apply()`, and `bind()`

## 1. Overview
In JavaScript, functions are first-class objects with built-in methods on their prototype (`Function.prototype`). When you want to **manually control what `this` points to**, JavaScript provides three explicit binding methods:

* **`call()`**: Invokes the function **immediately**, passing arguments **individually (comma-separated)**.
* **`apply()`**: Invokes the function **immediately**, passing arguments as an **array (or array-like object)**.
* **`bind()`**: Does **NOT** invoke immediately; returns a **brand-new function** with `this` permanently bound.

```
                      Explicit Binding Methods
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
      call()                  apply()                  bind()
 Invokes immediately    Invokes immediately     Returns new function
 (Args: a, b, c)         (Args: [a, b, c])       (For later execution)
```

---

## 2. The `call()` Method

### Syntax:
```javascript
func.call(thisArg, arg1, arg2, /* ..., */ argN)
```

### Code Example:
```javascript
const user1 = {
  firstName: "Aarav",
  lastName: "Sharma"
};

const user2 = {
  firstName: "Priya",
  lastName: "Patel"
};

function introduce(greeting, punctuation) {
  console.log(`${greeting}, I am${this.firstName} ${this.lastName}${punctuation}`);
}

// Explicitly bind `this` to user1
introduce.call(user1, "Hello", "!");

// Explicitly bind `this` to user2
introduce.call(user2, "Namaste", ".");
```

**Output:**
```text
Hello, I am Aarav Sharma!
Namaste, I am Priya Patel.
```

---

## 3. The `apply()` Method

`apply()` is identical to `call()`, except it accepts arguments as an array.

> **Memory Trick:** 
> * **C**all $\rightarrow$ **C**omma-separated arguments
> * **A**pply $\rightarrow$ **A**rray of arguments

### Syntax:
```javascript
func.apply(thisArg, [arg1, arg2, /* ..., */ argN])
```

### Code Example:
```javascript
const employee = {
  name: "Vikram",
  role: "Backend Engineer"
};

function logWorkHours(monday, tuesday, wednesday) {
  const total = monday + tuesday + wednesday;
  console.log(`${this.name} (${this.role}) worked${total} hours across 3 days.`);
}

// Passing arguments as an array
const hours = [8, 9, 7.5];
logWorkHours.apply(employee, hours);
```

**Output:**
```text
Vikram (Backend Engineer) worked 24.5 hours across 3 days.
```

---

## 4. The `bind()` Method

`bind()` does not execute the function right away. Instead, it returns a new copy of the function with `this` locked in.

### Syntax:
```javascript
const boundFunc = func.bind(thisArg, arg1, /* ..., */ argN)
```

### Code Example:
```javascript
const car = {
  brand: "Tata",
  model: "Harrier",
  getDetails() {
    return `${this.brand}${this.model}`;
  }
};

// ❌ Problem: Losing `this` reference when assigning method to variable
const unboundGetDetails = car.getDetails;
console.log("Unbound:", unboundGetDetails()); // undefined undefined

// ✅ Solution: Bind `this` permanently to `car`
const boundGetDetails = car.getDetails.bind(car);
console.log("Bound:", boundGetDetails());
```

**Output:**
```text
Unbound: undefined undefined
Bound: Tata Harrier
```

---

## 5. Practical Real-World Patterns

### A. Function Borrowing
Borrow a method from one object and run it on another object without copying code.

```javascript
const person = {
  fullName() {
    return `${this.firstName}${this.lastName}`;
  }
};

const customer = {
  firstName: "Rohan",
  lastName: "Verma"
};

// customer borrows the fullName method from person
const name = person.fullName.call(customer);
console.log("Borrowed Name:", name);
```

**Output:**
```text
Borrowed Name: Rohan Verma
```

---

### B. Function Currying & Partial Application with `bind()`
You can pre-set fixed initial arguments using `bind()`.

```javascript
function multiply(a, b) {
  return a * b;
}

// Create specialized versions by fixing the first argument `a`:
const double = multiply.bind(null, 2); // 'this' is not needed here, so pass null
const triple = multiply.bind(null, 3);

console.log("Double 7:", double(7));
console.log("Triple 7:", triple(7));
```

**Output:**
```text
Double 7: 14
Triple 7: 21
```

---

### C. Fixing `this` in Asynchronous Callbacks
When passing object methods into `setTimeout` or event listeners, `this` gets detached. `bind()` fixes it.

```javascript
const counter = {
  count: 0,
  increment() {
    this.count++;
    console.log("Counter value:", this.count);
  }
};

// Bind keeps the `counter` reference intact inside setTimeout
setTimeout(counter.increment.bind(counter), 50);
```

**Output:**
```text
Counter value: 1
```

---

## 6. Summary Comparison Table

| Feature | `call()` | `apply()` | `bind()` |
| :--- | :--- | :--- | :--- |
| **Execution** | **Immediately** | **Immediately** | **Returns a new function** |
| **Arguments format** | Comma-separated list (`a, b, c`) | Array (`[a, b, c]`) | Comma-separated list (can be preset) |
| **Return Value** | Result of the function call | Result of the function call | The bound function |
| **Can re-bind later?** | N/A | N/A | ❌ **No**, permanent once bound |

---

## 7. Common Interview Trap: Hard Binding Immutability

Once a function is bound via `.bind()`, its `this` context **cannot be overwritten** by another `.bind()`, `.call()`, or `.apply()`.

```javascript
function show() {
  console.log(this.name);
}

const obj1 = { name: "Object 1" };
const obj2 = { name: "Object 2" };

const boundTo1 = show.bind(obj1);

// Attempting to re-bind to obj2:
const boundTo2 = boundTo1.bind(obj2);
boundTo2(); // Still prints "Object 1"!

boundTo1.call(obj2); // Still prints "Object 1"!
```

**Output:**
```text
Object 1
Object 1
```
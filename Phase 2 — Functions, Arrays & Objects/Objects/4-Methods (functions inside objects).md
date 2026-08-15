# 07. Object Methods (Functions Inside Objects)

## 1. Overview
A **Method** is simply a function stored as a property inside an object. Methods define the **behavior** or actions an object can perform using its own data.

```javascript
const user = {
  name: "Aditya",
  // Method definition
  greet: function() {
    return "Hello, welcome back!";
  }
};

console.log(user.greet());
```

**Output:**
```text
Hello, welcome back!
```

---

## 2. Defining Methods: Old vs. ES6 Shorthand

### A. Pre-ES6 Syntax
```javascript
const calculator = {
  add: function(a, b) {
    return a + b;
  }
};

console.log(calculator.add(5, 3));
```

**Output:**
```text
8
```

### B. ES6 Concise Method Syntax (Recommended)
You can omit the `: function` keyword entirely:

```javascript
const calculator = {
  add(a, b) {
    return a + b;
  },
  multiply(a, b) {
    return a * b;
  }
};

console.log("Sum:", calculator.add(10, 4));
console.log("Product:", calculator.multiply(10, 4));
```

**Output:**
```text
Sum: 14
Product: 40
```

---

## 3. The `this` Keyword in Object Methods

Inside an object method, `this` refers to the **object that called the method** (the execution context to the left of the dot).

```javascript
const bankAccount = {
  owner: "Neha",
  balance: 5000,
  deposit(amount) {
    this.balance += amount;
    return `Deposited ₹${amount}. New balance: ₹${this.balance}`;
  },
  getBalance() {
    return `${this.owner}'s Balance: ₹${this.balance}`;
  }
};

console.log(bankAccount.deposit(1500));
console.log(bankAccount.getBalance());
```

**Output:**
```text
Deposited ₹1500. New balance: ₹6500
Neha's Balance: ₹6500
```

---

## 4. Arrow Functions vs. Regular Functions as Methods ⚠️

This is one of the most common JavaScript interview questions.

### Regular Functions:
* Have their own dynamic `this` bound to the object calling the function.

### Arrow Functions:
* **Do NOT have their own `this`**. They inherit `this` from the surrounding lexical scope (typically `window` or `{}` in Node.js).

```javascript
const person = {
  name: "Vikram",
  
  // Regular method: `this` points to `person`
  regularGreet() {
    return `Hello, I am ${this.name}`;
  },
  
  // ❌ Arrow function method: `this` does NOT point to `person`
  arrowGreet: () => {
    return `Hello, I am ${this.name}`;
  }
};

console.log("Regular Method:", person.regularGreet());
console.log("Arrow Method:", person.arrowGreet());
```

**Output:**
```text
Regular Method: Hello, I am Vikram
Arrow Method: Hello, I am undefined
```

> **Rule of Thumb:** Never use arrow functions directly as methods inside an object literal if you need access to `this`.

---

## 5. Adding Methods Dynamically

Just like any other property, methods can be added after an object has already been created.

```javascript
const robot = {
  model: "RX-9"
};

// Assign a function dynamically
robot.sayHello = function() {
  return `Robot ${this.model} online.`;
};

console.log(robot.sayHello());
```

**Output:**
```text
Robot RX-9 online.
```

---

## 6. Method Chaining

Method chaining occurs when methods return `this` (the object itself), allowing you to call multiple methods consecutively on a single line.

```javascript
const ladder = {
  step: 0,
  up() {
    this.step++;
    return this; // Return current object for chaining
  },
  down() {
    this.step--;
    return this;
  },
  showStep() {
    console.log("Current step:", this.step);
    return this;
  }
};

// Chaining calls:
ladder.up().up().up().down().showStep();
```

**Output:**
```text
Current step: 2
```

---

## 7. Using Built-in Object Methods with Objects

JavaScript provides powerful static methods on the `Object` constructor to inspect methods and properties:

```javascript
const user = {
  id: 1,
  name: "Pooja",
  login() { return "Logged in"; }
};

console.log("Keys:", Object.keys(user));
console.log("Has 'login' method?:", typeof user.login === "function");
```

**Output:**
```text
Keys: [ 'id', 'name', 'login' ]
Has 'login' method?: true
```

---

## 8. Quick Cheat Sheet

| Feature | Syntax | Best Use Case |
| :--- | :--- | :--- |
| **ES6 Method** | `methodName() { ... }` | Standard method declaration |
| **Access Data** | `this.propertyName` | Accessing object's own properties |
| **Arrow Function** | `() => { ... }` | **Avoid** for object methods using `this` |
| **Chaining** | `return this;` | Enables fluent API syntax `obj.step1().step2()` |
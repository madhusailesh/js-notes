# 01. Creating Objects Using Object Literals

## 1. Overview
An **Object Literal** is the simplest, most direct, and most common way to define and create an object in JavaScript. It is a comma-separated list of **`key: value` pairs** wrapped inside curly braces `{}`.

* **Keys (Properties):** Always strings or Symbols (quotes around keys are optional if they follow valid identifier rules).
* **Values:** Can be any valid data type (numbers, strings, booleans, arrays, functions, or other objects).

---

## 2. Basic Object Creation & Access

### Code Example
```javascript
const person = {
  firstName: "Alex",
  lastName: "Morgan",
  age: 24,
  isEmployed: true,
  skills: ["JavaScript", "React", "Node.js"]
};

// 1. Dot Notation (Preferred when key name is known)
console.log("First Name:", person.firstName);
console.log("Age:", person.age);

// 2. Bracket Notation (Required for dynamic keys or special characters)
console.log("Last Name:", person["lastName"]);
console.log("Primary Skill:", person.skills[0]);
```

**Output:**
```text
First Name: Alex
Age: 24
Last Name: Morgan
Primary Skill: JavaScript
```

---

## 3. Adding, Modifying, and Deleting Properties

Objects in JavaScript are mutable even if declared with `const` (the variable reference cannot change, but the object's contents can).

### Code Example
```javascript
const car = {
  brand: "Toyota",
  model: "Corolla"
};

// Add a new property
car.year = 2022;

// Modify an existing property
car.model = "Camry";

// Delete a property using the `delete` operator
delete car.brand;

console.log("Updated Car:", car);
```

**Output:**
```text
Updated Car: { model: 'Camry', year: 2022 }
```

---

## 4. Modern ES6+ Enhancements for Object Literals

ES6 introduced convenient shorthand features that make object literals much cleaner.

### A. Property Value Shorthand
If your variable name matches the object key name, you don't need to write `key: key`.

```javascript
const username = "dev_sam";
const role = "Admin";

// Old Way:
// const user = { username: username, role: role };

// ES6 Shorthand:
const user = { username, role };

console.log("User Object:", user);
```

**Output:**
```text
User Object: { username: 'dev_sam', role: 'Admin' }
```

---

### B. Method Definition Shorthand
You can drop the `: function` keyword when defining methods inside an object.

```javascript
const calculator = {
  // ES6 Method syntax
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  }
};

console.log("Sum:", calculator.add(10, 5));
console.log("Difference:", calculator.subtract(10, 5));
```

**Output:**
```text
Sum: 15
Difference: 5
```

---

### C. Computed Property Names
You can dynamically compute key names inside square brackets `[expression]` directly inside the literal.

```javascript
const keyPrefix = "user_";
const dynamicId = 101;

const profile = {
  [keyPrefix + dynamicId]: "John Doe",
  ["created" + "At"]: "2026-08-14"
};

console.log("Profile:", profile);
```

**Output:**
```text
Profile: { user_101: 'John Doe', createdAt: '2026-08-14' }
```

---

## 5. Using `this` Inside Object Methods

When a normal method is called on an object, `this` refers to the object itself.

```javascript
const user = {
  name: "Sarah",
  points: 40,
  getSummary() {
    return `${this.name} has${this.points} points.`;
  }
};

console.log(user.getSummary());
```

**Output:**
```text
Sarah has 40 points.
```

> **Warning (Arrow Functions in Objects):**
> Do not use arrow functions for object methods if you need `this`. Arrow functions inherit `this` from their surrounding lexical scope (usually `window` or `{}`), not the object itself.

---

## 6. Nested Objects

Objects can contain other objects as values.

```javascript
const company = {
  name: "TechCorp",
  location: {
    city: "Bengaluru",
    country: "India"
  },
  contact: {
    email: "info@techcorp.com"
  }
};

console.log("City:", company.location.city);
console.log("Email:", company.contact.email);
```

**Output:**
```text
City: Bengaluru
Email: info@techcorp.com
```

---

## 7. Checking If a Property Exists

```javascript
const student = {
  id: 1,
  grade: "A"
};

// Method 1: `in` operator
console.log("Has 'grade'?", "grade" in student);
console.log("Has 'age'?", "age" in student);

// Method 2: Object.hasOwn() (ES2022 standard)
console.log("Has own 'id'?", Object.hasOwn(student, "id"));
```

**Output:**
```text
Has 'grade'? true
Has 'age'? false
Has own 'id'? true
```

---

## 8. Quick Cheat Sheet

| Task | Syntax | Example |
| :--- | :--- | :--- |
| **Create** | `{ key: value }` | `const obj = { a: 1 };` |
| **Shorthand** | `{ varName }` | `const x = 5; { x };` $\rightarrow$ `{ x: 5 }` |
| **Method** | `{ fn() {} }` | `greet() { return "hi"; }` |
| **Dynamic Key** | `{ [expr]: val }` | `{ ["id_" + 1]: 100 }` |
| **Delete** | `delete obj.key` | `delete user.age;` |
| **Check Key** | `"key" in obj` | `"name" in user` $\rightarrow$ `true` |
# 21. ES6 Classes in JavaScript

## 1. Overview
Introduced in **ES6 (ECMAScript 2015)**, the `class` syntax provides a cleaner, more intuitive Object-Oriented Programming (OOP) syntax in JavaScript. 

> **Important Truth:** JavaScript classes are **not** real object-oriented classes like in Java or C++. They are **syntactic sugar** over JavaScript's existing **prototypal inheritance** model.

---

## 2. Basic Class Syntax & Constructor

* **`constructor()`**: The special method automatically executed when a new object instance is created with the `new` keyword.
* **Instance Methods**: Methods written inside the class body are automatically placed on the class's **`prototype`**, sharing memory across all instances.

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // Instance method (lives on User.prototype)
  getInfo() {
    return `${this.name} (${this.email})`;
  }
}

const user1 = new User("Aarav", "aarav@example.com");

console.log(user1.getInfo());
console.log("Is method on prototype?:", Object.hasOwn(User.prototype, "getInfo"));
```

**Output:**
```text
Aarav (aarav@example.com)
Is method on prototype?: true
```

---

## 3. Inheritance with `extends` and `super`

* **`extends`**: Links the prototype of the child class to the parent class.
* **`super()`**: Calls the constructor of the parent class. You **must** call `super()` in the derived constructor before accessing `this`.

```javascript
class Vehicle {
  constructor(brand, speed) {
    this.brand = brand;
    this.speed = speed;
  }

  accelerate() {
    return `${this.brand} is moving at${this.speed} km/h.`;
  }
}

class ElectricCar extends Vehicle {
  constructor(brand, speed, batteryCapacity) {
    // 1. Call parent constructor
    super(brand, speed);
    this.batteryCapacity = batteryCapacity;
  }

  // Method overriding with super call
  accelerate() {
    return `${super.accelerate()} (Battery:${this.batteryCapacity} kWh)`;
  }
}

const myTesla = new ElectricCar("Tesla", 120, 75);

console.log(myTesla.accelerate());
console.log("Is myTesla a Vehicle?:", myTesla instanceof Vehicle);
console.log("Is myTesla an ElectricCar?:", myTesla instanceof ElectricCar);
```

**Output:**
```text
Tesla is moving at 120 km/h. (Battery: 75 kWh)
Is myTesla a Vehicle?: true
Is myTesla an ElectricCar?: true
```

---

## 4. Static Methods and Static Properties

* Declared using the **`static`** keyword.
* Belong directly to the **Class itself**, **NOT** to the object instances.
* Commonly used for utility/helper functions or global caches.

```javascript
class MathUtils {
  static PI = 3.14159;

  static calculateCircleArea(radius) {
    return this.PI * radius * radius;
  }
}

// Call directly on the Class:
console.log("Static PI:", MathUtils.PI);
console.log("Circle Area (r=5):", MathUtils.calculateCircleArea(5));

// ❌ Cannot be called on instances:
const calc = new MathUtils();
console.log("Instance access:", calc.calculateCircleArea);
```

**Output:**
```text
Static PI: 3.14159
Circle Area (r=5): 78.53975
Instance access: undefined
```

---

## 5. Getters and Setters (`get` / `set`)

Getters and setters allow you to intercept and control property access and assignment, validating data before saving it.

```javascript
class Account {
  constructor(owner, initialBalance) {
    this.owner = owner;
    this._balance = initialBalance; // Private convention (_)
  }

  // Getter
  get balance() {
    return `₹${this._balance}`;
  }

  // Setter
  set balance(newAmount) {
    if (newAmount < 0) {
      console.log("Error: Balance cannot be negative!");
      return;
    }
    this._balance = newAmount;
  }
}

const myAcc = new Account("Pooja", 5000);

console.log("Current Balance:", myAcc.balance); // Accessed like a property (no ())

myAcc.balance = 8000;  // Triggers setter
console.log("Updated Balance:", myAcc.balance);

myAcc.balance = -200;  // Rejected by validation
```

**Output:**
```text
Current Balance: ₹5000
Updated Balance: ₹8000
Error: Balance cannot be negative!
```

---

## 6. Truly Private Fields and Methods (`#`)

Modern JavaScript (ES2022+) provides native private fields and methods using the **`#`** prefix.
* Completely inaccessible from outside the class instance.
* Trying to access private fields directly throws a **`SyntaxError`**.

```javascript
class BankWallet {
  // 1. Declare private field
  #pin;
  #balance;

  constructor(pin, balance) {
    this.#pin = pin;
    this.#balance = balance;
  }

  // Private helper method
  #validatePin(enteredPin) {
    return this.#pin === enteredPin;
  }

  // Public interface
  checkBalance(enteredPin) {
    if (!this.#validatePin(enteredPin)) {
      return "Access Denied: Invalid PIN";
    }
    return `Secure Balance: ₹${this.#balance}`;
  }
}

const wallet = new BankWallet(1234, 15000);

console.log(wallet.checkBalance(1234));
console.log(wallet.checkBalance(9999));

// ❌ Direct access is blocked:
// console.log(wallet.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```

**Output:**
```text
Secure Balance: ₹15000
Access Denied: Invalid PIN
```

---

## 7. The Big Reveal: Classes are Syntactic Sugar (Proof by Example)

Under the hood, an ES6 `class` compiles into standard **Constructor Functions + Prototype assignments**.

### ES6 Class Syntax:
```javascript
class Developer {
  constructor(name) {
    this.name = name;
  }
  code() {
    return `${this.name} is coding`;
  }
}
```

### Exact Equivalent in Pre-ES6 Prototypal JavaScript:
```javascript
function Developer(name) {
  this.name = name;
}

Developer.prototype.code = function() {
  return `${this.name} is coding`;
};
```

### Direct Proof:
```javascript
class TestClass {}

// 1. A class is literally a function!
console.log("typeof TestClass:", typeof TestClass);

// 2. Methods exist on the prototype object!
console.log("Is prototype an object?:", typeof TestClass.prototype);
console.log("Prototype constructor matches?:", TestClass.prototype.constructor === TestClass);
```

**Output:**
```text
typeof TestClass: function
Is prototype an object?: object
Prototype constructor matches?: true
```

---

## 8. Summary Comparison: `class` vs. Constructor Functions

| Feature | ES6 `class` | Constructor Function |
| :--- | :--- | :--- |
| **Underlying Engine** | Prototype Chain | Prototype Chain |
| **`typeof` check** | `"function"` | `"function"` |
| **Must use `new`?** | ✅ **Yes** (Throws `TypeError` if called without `new`) | ⚠️ No (Needs manual `new.target` check) |
| **Hoisting** | ❌ Block-scoped (in TDZ) | ✅ Fully hoisted |
| **Strict Mode** | ✅ Enabled by default in class body | ⚠️ Optional (must declare `"use strict"`) |
| **Methods enumerable?** | ❌ Non-enumerable (cleaner iteration) | ⚠️ Enumerable by default |
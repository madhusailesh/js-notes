# 20. Prototypes & Prototypal Inheritance in JavaScript

## 1. What is a Prototype?

In JavaScript, objects have an internal, hidden link to another object called their **Prototype**. 

When you attempt to read a property or call a method on an object, JavaScript first looks on the object itself. If it doesn't find it, it automatically checks the object's prototype. This mechanism is called **Prototypal Inheritance**.

```
+--------------------------+
|          child           |
|   name: "Alex"           |
|   [[Prototype]] ─────────┼───────► +--------------------------+
+--------------------------+         |          parent          |
                                     |   city: "Bengaluru"      |
                                     |   [[Prototype]] ─────────┼───────► Object.prototype ──► null
                                     +--------------------------+
```

---

## 2. The Most Confusing Pair: `prototype` vs `__proto__`

This is one of the most frequently asked JavaScript interview questions.

```
       Function / Constructor                       Instantiated Object
     +-------------------------+                 +-------------------------+
     |   function Person()     |                 |  const p1 = new Person()|
     |                         |                 |                         |
     |   .prototype property   | ──Points to──►  |  .__proto__ link        |
     +-------------------------+                 +-------------------------+
```

### A. `prototype` (Exists ONLY on Functions)
* A blueprint object attached automatically to **constructor functions** and standard `function` declarations.
* It defines properties and methods that will be shared by all instances created with `new FunctionName()`.

### B. `__proto__` (or `[[Prototype]]`) (Exists on EVERY Object)
* The actual **internal reference link** on an object instance pointing to its prototype object.
* Modern standard: Use `Object.getPrototypeOf(obj)` and `Object.setPrototypeOf(obj)` instead of accessing `__proto__` directly.

### Code Verification:
```javascript
function User(name) {
  this.name = name;
}

// Attach a shared method to the constructor's prototype
User.prototype.sayHi = function() {
  return `Hi, I am ${this.name}`;
};

const user1 = new User("Aarav");

console.log("Is user1.__proto__ === User.prototype?:", Object.getPrototypeOf(user1) === User.prototype);
console.log("Does User function have .prototype?:", typeof User.prototype);
console.log("Does user1 instance have .prototype?:", typeof user1.prototype);
console.log(user1.sayHi());
```

**Output:**
```text
Is user1.__proto__ === User.prototype?: true
Does User function have .prototype?: object
Does user1 instance have .prototype?: undefined
Hi, I am Aarav
```

---

## 3. The Prototype Chain (Lookup Mechanism)

When you access `obj.someProp`:
1. Check `obj`'s own properties.
2. If not found, check `obj.__proto__` (`Constructor.prototype`).
3. If not found, follow the chain upwards to `Object.prototype`.
4. If still not found, `Object.prototype.__proto__` is **`null`**, terminating the search and returning `undefined`.

```
user1 (instance)
  └── __proto__ ──► User.prototype
                        └── __proto__ ──► Object.prototype
                                              └── __proto__ ──► null (End of chain)
```

### Code Example:
```javascript
const numbers = [1, 2, 3];

// Array methods like .push() or .map() come from Array.prototype!
console.log("Array has own property 'map'?:", Object.hasOwn(numbers, "map"));
console.log("Array prototype has 'map'?:", Object.hasOwn(Array.prototype, "map"));
console.log("End of Chain:", Object.getPrototypeOf(Object.prototype));
```

**Output:**
```text
Array has own property 'map'?: false
Array prototype has 'map'?: true
End of Chain: null
```

---

## 4. Pure Prototypal Inheritance with `Object.create()`

`Object.create(protoObject)` creates a brand-new, clean object and sets its internal `[[Prototype]]` directly to `protoObject`.

```javascript
const vehicle = {
  wheels: 4,
  start() {
    return `Vehicle with ${this.wheels} wheels is starting...`;
  }
};

// Create `car` inheriting from `vehicle`
const car = Object.create(vehicle);
car.brand = "Mahindra";

console.log("Brand (Own property):", car.brand);
console.log("Wheels (Inherited):", car.wheels);
console.log("Method (Inherited):", car.start());
console.log("Is vehicle the prototype of car?:", Object.getPrototypeOf(car) === vehicle);
```

**Output:**
```text
Brand (Own property): Mahindra
Wheels (Inherited): 4
Method (Inherited): Vehicle with 4 wheels is starting...
Is vehicle the prototype of car?: true
```

---

## 5. Constructor Functions & Inheritance (Pre-ES6 Classes)

Before `class` syntax was introduced in ES6, developers built object-oriented hierarchies using Constructor Functions and Prototype links.

```javascript
// 1. Parent Constructor
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  return `${this.name} is eating.`;
};

// 2. Child Constructor
function Dog(name, breed) {
  // Call super constructor to initialize parent properties
  Animal.call(this, name);
  this.breed = breed;
}

// 3. Link prototypes (Inherit methods from Animal)
Dog.prototype = Object.create(Animal.prototype);

// 4. Reset constructor property back to Dog
Dog.prototype.constructor = Dog;

// 5. Add Dog-specific method
Dog.prototype.bark = function() {
  return `${this.name} says Woof!`;
};

const myDog = new Dog("Sheru", "Indie");

console.log(myDog.eat());  // Inherited from Animal.prototype
console.log(myDog.bark()); // Found on Dog.prototype
console.log("Is myDog an Animal?:", myDog instanceof Animal);
console.log("Is myDog a Dog?:", myDog instanceof Dog);
```

**Output:**
```text
Sheru is eating.
Sheru says Woof!
Is myDog an Animal?: true
Is myDog a Dog?: true
```

---

## 6. Why Attach Methods to `.prototype` Instead of `this`?

```javascript
// ❌ Bad Memory Pattern: Method recreated for every new instance
function BadUser(name) {
  this.name = name;
  this.greet = function() { return "Hi!"; }; // Recreated 10,000 times!
}

// ✅ Efficient Pattern: Method exists once in shared memory
function GoodUser(name) {
  this.name = name;
}
GoodUser.prototype.greet = function() { return "Hi!"; }; // Only 1 function in memory
```

---

## 7. Summary Comparison Table

| Property / Method | Belongs To | Purpose |
| :--- | :--- | :--- |
| **`prototype`** | Functions only | The blueprint object used when `new` is called |
| **`__proto__`** | All Objects | Internal pointer to the object's prototype |
| **`Object.getPrototypeOf(obj)`** | Global `Object` | Standard, safe way to read an object's prototype |
| **`Object.create(proto)`** | Global `Object` | Creates a new object with specified prototype |
| **`instanceof`** | Operator | Checks if `Constructor.prototype` exists in object's chain |
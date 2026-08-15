# 10. Built-in Object Methods (Inspection & Immutability)

## 1. Overview
JavaScript provides several static utility methods on the global `Object` constructor. They are divided into two main categories:
1. **Inspection / Iteration Methods:** `Object.keys()`, `Object.values()`, and `Object.entries()`
2. **Manipulation & Immutability Methods:** `Object.assign()`, `Object.freeze()`, and `Object.seal()`

---

## 2. Object Inspection: Keys, Values & Entries

These three methods extract properties from an object and convert them into arrays for easy iteration using `.map()`, `.filter()`, or `for...of`.

```
         ┌───────────────────────────┐
         │ { name: "Aarav", age: 24 }│
         └─────────────┬─────────────┘
      ┌────────────────┼────────────────┐
      ▼                ▼                ▼
 Object.keys()   Object.values()  Object.entries()
["name", "age"]   ["Aarav", 24]   [["name","Aarav"], ["age",24]]
```

### A. `Object.keys(obj)`
Returns an array of all **enumerable property names (keys)** as strings.

```javascript
const user = {
  id: 101,
  name: "Aarav",
  role: "Engineer"
};

const keys = Object.keys(user);
console.log("Keys:", keys);
console.log("Number of properties:", keys.length);
```

**Output:**
```text
Keys: [ 'id', 'name', 'role' ]
Number of properties: 3
```

---

### B. `Object.values(obj)`
Returns an array of all **enumerable property values**.

```javascript
const user = {
  id: 101,
  name: "Aarav",
  role: "Engineer"
};

const values = Object.values(user);
console.log("Values:", values);
```

**Output:**
```text
Values: [ 101, 'Aarav', 'Engineer' ]
```

---

### C. `Object.entries(obj)`
Returns an array of an object's key-value pairs as **`[key, value]` sub-arrays**.

```javascript
const user = {
  id: 101,
  name: "Aarav"
};

const entries = Object.entries(user);
console.log("Entries:", entries);

// Iterating cleanly using array destructuring:
for (const [key, value] of Object.entries(user)) {
  console.log(`Field ${key} has value:${value}`);
}
```

**Output:**
```text
Entries: [ [ 'id', 101 ], [ 'name', 'Aarav' ] ]
Field id has value: 101
Field name has value: Aarav
```

#### Reconstructing Objects with `Object.fromEntries()`:
You can transform object entries and turn them back into an object:
```javascript
const prices = { apple: 50, banana: 20, mango: 80 };

// Double all prices:
const doubled = Object.fromEntries(
  Object.entries(prices).map(([fruit, price]) => [fruit, price * 2])
);

console.log("Doubled Prices:", doubled);
```

**Output:**
```text
Doubled Prices: { apple: 100, banana: 40, mango: 160 }
```

---

## 3. Object Merging: `Object.assign()`

`Object.assign(target, ...sources)` copies all enumerable own properties from one or more source objects to a target object and returns the mutated target.

### Code Example:
```javascript
const baseConfig = { theme: "light", debug: false };
const userConfig = { theme: "dark", lang: "en" };

// Merging into a brand-new target object {}
const finalConfig = Object.assign({}, baseConfig, userConfig);

console.log("Final Config:", finalConfig);
console.log("Base Config preserved:", baseConfig);
```

**Output:**
```text
Final Config: { theme: 'dark', debug: false, lang: 'en' }
Base Config preserved: { theme: 'light', debug: false }
```

> **Note:** Modern JavaScript typically uses the object spread operator (`{ ...baseConfig, ...userConfig }`), but `Object.assign()` is still widely used and serves as the ES5 underlying mechanism.

---

## 4. Immutability Methods: `Object.seal()` vs. `Object.freeze()`

Both methods enforce levels of object protection.

```
                  Can Add?   Can Delete?   Can Modify Existing?
Normal Object        ✅          ✅                 ✅
Object.seal()        ❌          ❌                 ✅
Object.freeze()      ❌          ❌                 ❌
```

---

### A. `Object.seal(obj)` (Sealed / Locked Structure)
* **Allowed:** Modifying existing property values.
* **Not Allowed:** Adding new properties or deleting existing ones.

```javascript
const sealedAccount = {
  accountNumber: 987654,
  balance: 10000
};

Object.seal(sealedAccount);

// 1. Update existing -> Allowed ✅
sealedAccount.balance = 12000;

// 2. Add new -> Ignored (throws in 'use strict') ❌
sealedAccount.branch = "Downtown";

// 3. Delete existing -> Ignored (returns false) ❌
delete sealedAccount.accountNumber;

console.log("Sealed Account:", sealedAccount);
console.log("Is Sealed?", Object.isSealed(sealedAccount));
```

**Output:**
```text
Sealed Account: { accountNumber: 987654, balance: 12000 }
Is Sealed? true
```

---

### B. `Object.freeze(obj)` (Completely Read-Only)
* **Not Allowed:** Adding, updating, or deleting any properties.

```javascript
const frozenConfig = {
  apiUrl: "[https://api.domain.com](https://api.domain.com)",
  timeout: 3000
};

Object.freeze(frozenConfig);

// 1. Attempt Update -> Ignored ❌
frozenConfig.timeout = 5000;

// 2. Attempt Add -> Ignored ❌
frozenConfig.retryCount = 3;

// 3. Attempt Delete -> Ignored ❌
delete frozenConfig.apiUrl;

console.log("Frozen Config:", frozenConfig);
console.log("Is Frozen?", Object.isFrozen(frozenConfig));
```

**Output:**
```text
Frozen Config: { apiUrl: '[https://api.domain.com](https://api.domain.com)', timeout: 3000 }
Is Frozen? true
```

---

## 5. Critical Warning: Shallow Freeze / Seal ⚠️

Both `Object.freeze()` and `Object.seal()` are **shallow**. Nested objects inside a frozen object are still mutable!

```javascript
const user = {
  name: "Tanvi",
  preferences: {
    theme: "light"
  }
};

Object.freeze(user);

// user.name = "Priya"; // ❌ Prevented (Top level is frozen)

// ✅ Nested object CAN still be mutated!
user.preferences.theme = "dark";

console.log("Nested theme mutated:", user.preferences.theme);
```

**Output:**
```text
Nested theme mutated: dark
```

### Deep Freeze Helper Function:
```javascript
function deepFreeze(obj) {
  Object.keys(obj).forEach((prop) => {
    if (typeof obj[prop] === "object" && obj[prop] !== null) {
      deepFreeze(obj[prop]);
    }
  });
  return Object.freeze(obj);
}

const secureConfig = deepFreeze({ server: { port: 8080 } });
// secureConfig.server.port = 9000; // Silently fails or throws in strict mode
```

---

## 6. Quick Reference Summary

| Method | Returns | Key Capability |
| :--- | :--- | :--- |
| `Object.keys(obj)` | `Array<string>` | Extracts all key names |
| `Object.values(obj)` | `Array<any>` | Extracts all property values |
| `Object.entries(obj)` | `Array<[key, value]>` | Converts object into iterable pairs |
| `Object.assign(t, ...s)` | `Target Object` | Shallow merges source objects into target |
| `Object.seal(obj)` | `Object` | Prevents adding/deleting; allows value edits |
| `Object.freeze(obj)` | `Object` | Makes entire top-level object read-only |
| `Object.isFrozen(obj)` | `Boolean` | Checks if object is frozen |
| `Object.isSealed(obj)` | `Boolean` | Checks if object is sealed |
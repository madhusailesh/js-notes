# 06. Adding, Updating, and Deleting Object Properties

## 1. Overview
In JavaScript, objects are **mutable by default**—even if declared with `const`. While you cannot reassign a `const` object variable to a new memory reference, you can freely **add**, **modify (update)**, and **delete** properties inside that object.

---

## 2. Adding & Updating Properties

Adding a new property and updating an existing one use the exact same assignment syntax (`=`):
* If the key **does not exist**, JavaScript **adds** it.
* If the key **already exists**, JavaScript **overwrites (updates)** its value.

### Code Example:
```javascript
const user = {
  id: 101,
  name: "Karan",
  city: "Mumbai"
};

// --- UPDATING EXISTING PROPERTIES ---
user.city = "Pune";           // Dot notation
user["name"] = "Karan Sharma"; // Bracket notation

// --- ADDING NEW PROPERTIES ---
user.role = "Software Engineer";
user["isVerified"] = true;

// Adding dynamic key via variable
const statusKey = "accountStatus";
user[statusKey] = "Active";

console.log("Updated User Object:", user);
```

**Output:**
```text
Updated User Object: {
  id: 101,
  name: 'Karan Sharma',
  city: 'Pune',
  role: 'Software Engineer',
  isVerified: true,
  accountStatus: 'Active'
}
```

---

## 3. Deleting Properties (`delete` Operator)

The `delete` operator removes a property (both the key and its value) completely from an object.

### A. Basic Deletion
```javascript
const laptop = {
  brand: "Dell",
  ram: "16GB",
  storage: "512GB",
  inStock: false
};

// Delete using dot notation
delete laptop.inStock;

// Delete using bracket notation
delete laptop["storage"];

console.log("Laptop after deletion:", laptop);
```

**Output:**
```text
Laptop after deletion: { brand: 'Dell', ram: '16GB' }
```

---

### B. What Does `delete` Return?
The `delete` operator evaluates to a boolean:
* Returns `true` if the property was successfully deleted or if the property **never existed**.
* Returns `false` only if the property is **non-configurable** (e.g., created via `Object.defineProperty` with `configurable: false`).

```javascript
const item = { price: 100 };

console.log(delete item.price);        // true (deleted)
console.log(delete item.nonExistent);  // true (does nothing, but returns true)
console.log(item);                     // {}
```

**Output:**
```text
true
true
{}
```

---

## 4. Common Mistake: Setting to `undefined` vs. `delete`

Setting a property to `undefined` or `null` is **not** the same as deleting it.

```javascript
const student = {
  name: "Riya",
  score: 95
};

// ❌ Setting to undefined keeps the key in memory:
student.score = undefined;

console.log("Has 'score' key? (via in operator):", "score" in student);
console.log("Object Keys:", Object.keys(student));

// ✅ Using delete removes the key entirely:
delete student.score;

console.log("Has 'score' key after delete?:", "score" in student);
console.log("Object Keys after delete:", Object.keys(student));
```

**Output:**
```text
Has 'score' key? (via in operator): true
Object Keys: [ 'name', 'score' ]
Has 'score' key after delete?: false
Object Keys after delete: [ 'name' ]
```

---

## 5. Preventing Modifications (`Object.freeze` vs `Object.seal`)

If you want to protect an object from being modified or deleted, JavaScript provides built-in methods:

### A. `Object.seal()`
* **Can:** Update existing values.
* **Cannot:** Add new properties or delete existing ones.

```javascript
const sealedCar = { brand: "Ford", model: "Mustang" };
Object.seal(sealedCar);

sealedCar.model = "GT"; // ✅ Allowed (update)
sealedCar.year = 2024;  // ❌ Silently ignored (cannot add)
delete sealedCar.brand; // ❌ Silently ignored (cannot delete)

console.log("Sealed Object:", sealedCar);
```

**Output:**
```text
Sealed Object: { brand: 'Ford', model: 'GT' }
```

---

### B. `Object.freeze()` (Read-Only)
* **Cannot:** Add new properties, update existing properties, or delete properties.

```javascript
const frozenConfig = { apiEndpoint: "[https://api.example.com](https://api.example.com)", timeout: 5000 };
Object.freeze(frozenConfig);

frozenConfig.timeout = 10000;  // ❌ Ignored / Error in strict mode
frozenConfig.version = "v1";   // ❌ Ignored
delete frozenConfig.timeout;   // ❌ Ignored

console.log("Frozen Object:", frozenConfig);
```

**Output:**
```text
Frozen Object: { apiEndpoint: '[https://api.example.com](https://api.example.com)', timeout: 5000 }
```

---

## 6. Modern Immutable Pattern (Using Rest/Destructuring)

In modern frontend frameworks like React, mutating objects directly with `delete` is discouraged because it breaks pure state tracking. Instead, omit properties immutably:

```javascript
const originalUser = {
  id: 1,
  name: "Aman",
  passwordHash: "secret12345",
  email: "aman@example.com"
};

// Remove passwordHash without modifying originalUser:
const { passwordHash, ...cleanUser } = originalUser;

console.log("Clean User:", cleanUser);
console.log("Original User preserved:", originalUser.passwordHash);
```

**Output:**
```text
Clean User: { id: 1, name: 'Aman', email: 'aman@example.com' }
Original User preserved: secret12345
```

---

## 7. Quick Reference Cheat Sheet

| Action | Syntax | Notes |
| :--- | :--- | :--- |
| **Add / Update** | `obj.key = val` or `obj["key"] = val` | Creates key if absent; overwrites if present |
| **Delete** | `delete obj.key` | Removes key and value completely from memory |
| **Check Existence** | `'key' in obj` | Returns `true` even if value is `undefined` |
| **Lock (No Add/Delete)** | `Object.seal(obj)` | Allows updates to existing properties |
| **Full Lock (Read-Only)**| `Object.freeze(obj)` | Completely immutable (shallow) |
| **Immutable Remove** | `const { key, ...rest } = obj` | Best practice in React / functional JS |
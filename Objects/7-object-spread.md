# 10. Spread Operator (`...`) with Objects

## 1. Overview
Introduced in **ES2018 (ES9)**, the **Object Spread Operator** (`...`) allows you to expand the enumerable properties of an existing object into a new object literal.

It is primarily used for:
* **Cloning / Copying** objects without mutating the original.
* **Merging** multiple objects into one.
* **Overriding / Updating** specific properties immutably (a core pattern in React & Redux).

---

## 2. Cloning / Copying Objects (Shallow Copy)

Instead of passing objects by reference (which mutates the original), spread creates a new independent object containing the same top-level properties.

```javascript
const originalUser = {
  id: 1,
  name: "Rohan",
  role: "User"
};

// Create a separate shallow copy
const clonedUser = { ...originalUser };

clonedUser.name = "Rohan Verma";

console.log("Original User Name:", originalUser.name);
console.log("Cloned User Name:", clonedUser.name);
```

**Output:**
```text
Original User Name: Rohan
Cloned User Name: Rohan Verma
```

---

## 3. Merging Multiple Objects

Combine two or more objects into a single object in a clean, readable line.

```javascript
const personalInfo = {
  name: "Alok",
  age: 26
};

const jobInfo = {
  role: "Full Stack Engineer",
  company: "CodeCraft"
};

const locationInfo = {
  city: "Bhubaneswar",
  country: "India"
};

// Merge all three objects
const completeProfile = { ...personalInfo, ...jobInfo, ...locationInfo };

console.log(completeProfile);
```

**Output:**
```text
{
  name: 'Alok',
  age: 26,
  role: 'Full Stack Engineer',
  company: 'CodeCraft',
  city: 'Bhubaneswar',
  country: 'India'
}
```

---

## 4. Overriding Properties & Default Configurations

> **Critical Rule:** In object spread, **order of appearance matters**. If duplicate keys exist, the **last key defined wins**.

### A. Overriding an Existing Property
```javascript
const baseConfig = {
  env: "production",
  port: 8080,
  debug: false
};

// Spread first, then overwrite `port` and `debug`
const devConfig = {
  ...baseConfig,
  port: 3000,
  debug: true
};

console.log(devConfig);
```

**Output:**
```text
{ env: 'production', port: 3000, debug: true }
```

---

### B. Setting Default Configurations (Defaults First)
Place default values at the beginning so incoming props override them:

```javascript
function createNotification(userOptions) {
  const defaultOptions = {
    duration: 3000,
    theme: "light",
    position: "top-right"
  };

  // userOptions will overwrite matching default properties
  return { ...defaultOptions, ...userOptions };
}

const toast = createNotification({ theme: "dark", duration: 5000 });
console.log(toast);
```

**Output:**
```text
{ duration: 5000, theme: 'dark', position: 'top-right' }
```

---

### C. Mistake: Order Reversal (Accidental Overwrite)
If you place the spread **after** your custom key, the spread will overwrite your changes:

```javascript
const original = { theme: "light" };

const brokenOverride = {
  theme: "dark",     // Defined first
  ...original        // Overwrites theme back to "light"!
};

console.log("Broken Theme:", brokenOverride.theme);
```

**Output:**
```text
Broken Theme: light
```

---

## 5. Adding Conditional Properties using Spread

You can spread conditional properties cleanly using short-circuit evaluation (`&&`):

```javascript
const isAdmin = true;
const hasDiscount = false;

const userSession = {
  id: 101,
  name: "Preeti",
  ...(isAdmin && { permissions: ["CREATE", "DELETE", "UPDATE"] }),
  ...(hasDiscount && { discountPercent: 20 })
};

console.log(userSession);
```

**Output:**
```text
{
  id: 101,
  name: 'Preeti',
  permissions: [ 'CREATE', 'DELETE', 'UPDATE' ]
}
```

---

## 6. The Shallow Copy Trap (Nested Objects) ⚠️

Spread creates a **shallow copy**. Top-level primitive values are copied by value, but nested objects/arrays are copied by **reference**.

```javascript
const originalData = {
  title: "Dashboard",
  dimensions: {
    width: 1920,
    height: 1080
  }
};

const shallowCopy = { ...originalData };

// Modifying nested property
shallowCopy.dimensions.width = 1280;

// Notice the original object also got modified!
console.log("Original width:", originalData.dimensions.width);
console.log("Shallow copy width:", shallowCopy.dimensions.width);
```

**Output:**
```text
Original width: 1280
Shallow copy width: 1280
```

### Solutions for Nested Copies:
1. **Manual Nested Spread:**
   ```javascript
   const safeCopy = {
     ...originalData,
     dimensions: { ...originalData.dimensions }
   };
   ```
2. **Modern Deep Clone:**
   ```javascript
   const deepClone = structuredClone(originalData);
   ```

---

## 7. Spread vs. `Object.assign()`

| Feature | Object Spread (`{ ...a, ...b }`) | `Object.assign({}, a, b)` |
| :--- | :--- | :--- |
| **Syntax** | Modern, declarative, concise | Functional, older (ES6) |
| **Mutates target?** | Creates new object literal | Mutates the first argument if not passed `{}` |
| **Setters execution** | Triggers object definitions directly | Invokes setters on the target object |

---

## 8. Quick Cheat Sheet

| Use Case | Syntax | Example Result |
| :--- | :--- | :--- |
| **Clone** | `const copy = { ...obj };` | Independent top-level copy |
| **Merge** | `const combined = { ...obj1, ...obj2 };` | Combines properties |
| **Update Field**| `const updated = { ...obj, key: newVal };` | Immutably updates `key` |
| **Conditional** | `{ ...obj, ...(cond && { key: val }) }` | Injects property only if condition is true |
| **Deep Copy** | `structuredClone(obj)` | Full independent hierarchy copy |
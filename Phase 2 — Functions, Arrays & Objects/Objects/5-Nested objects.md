# 08. Nested Objects in JavaScript

## 1. Overview
A **Nested Object** is an object stored as the value of a property inside another object. This hierarchical structure is essential for modeling complex real-world data, such as API responses, user profiles, and configuration trees.

```
user
 ├── name: "Rahul Sharma"
 ├── address (nested object)
 │    ├── city: "Bengaluru"
 │    └── geo (deeply nested object)
 │         ├── lat: 12.9716
 │         └── lng: 77.5946
 └── preferences (nested object)
      └── theme: "dark"
```

---

## 2. Creating and Accessing Nested Properties

You can navigate nested levels by chaining **dot notation**, **bracket notation**, or a mix of both.

```javascript
const student = {
  id: 101,
  name: "Ananya",
  academics: {
    branch: "Computer Science",
    semester: 4,
    marks: {
      math: 92,
      dsa: 95
    }
  },
  "contact-details": {
    email: "ananya@example.com"
  }
};

// Dot chaining
console.log("Branch:", student.academics.branch);
console.log("DSA Score:", student.academics.marks.dsa);

// Mixed dot and bracket chaining
console.log("Email:", student["contact-details"].email);
```

**Output:**
```text
Branch: Computer Science
DSA Score: 95
Email: ananya@example.com
```

---

## 3. Adding, Updating, and Deleting Nested Properties

Target the exact path to modify or delete inner properties.

```javascript
const company = {
  name: "NexTech",
  location: {
    city: "Hyderabad",
    country: "India"
  }
};

// 1. Update an existing nested property
company.location.city = "Cyberabad";

// 2. Add a new property inside the nested object
company.location.pincode = "500081";

// 3. Add a new nested object entirely
company.headcount = {
  engineering: 120,
  marketing: 30
};

// 4. Delete a nested property
delete company.location.country;

console.log("Updated Company:", JSON.stringify(company, null, 2));
```

**Output:**
```text
Updated Company: {
  "name": "NexTech",
  "location": {
    "city": "Cyberabad",
    "pincode": "500081"
  },
  "headcount": {
    "engineering": 120,
    "marketing": 30
  }
}
```

---

## 4. Safe Navigation: Preventing `TypeError`

Accessing properties on an `undefined` parent throws a runtime error that crashes your application.

### The Problem:
```javascript
const user = { name: "Aman" };

// ❌ Throws: TypeError: Cannot read properties of undefined (reading 'city')
// console.log(user.address.city);
```

### Solution A: Optional Chaining (`?.`) (Modern ES2020 Standard)
If any step in the chain is `null` or `undefined`, execution stops and evaluates safely to `undefined`.

```javascript
const user = {
  name: "Aman",
  profile: null
};

// Safe access on missing keys
console.log("City:", user.address?.city);
console.log("Bio:", user.profile?.bio);

// Combined with Nullish Coalescing (??) for fallbacks
const userCity = user.address?.city ?? "Not Provided";
console.log("City with fallback:", userCity);
```

**Output:**
```text
City: undefined
Bio: undefined
City with fallback: Not Provided
```

---

## 5. Nested Object Destructuring

Extract deeply nested properties directly into standalone variables.

```javascript
const developer = {
  id: 42,
  personal: {
    fullName: "Siddharth",
    social: {
      github: "sid_dev",
      twitter: "@sid_tweets"
    }
  }
};

// Destructure nested properties directly
const {
  personal: {
    fullName,
    social: { github }
  }
} = developer;

console.log("Full Name:", fullName);
console.log("GitHub:", github);
```

**Output:**
```text
Full Name: Siddharth
GitHub: sid_dev
```

#### Renaming and Default Values in Nested Destructuring:
```javascript
const config = {
  server: {
    port: 8080
  }
};

const {
  server: { port: serverPort, host = "localhost" }
} = config;

console.log("Port:", serverPort);
console.log("Host:", host);
```

**Output:**
```text
Port: 8080
Host: localhost
```

---

## 6. Copying Nested Objects: Shallow vs. Deep Clone ⚠️

A common interview trap: Spread syntax (`...`) and `Object.assign()` only perform a **shallow copy**—nested objects will still share their memory reference!

```javascript
const original = {
  title: "Admin Panel",
  settings: {
    theme: "light",
    notifications: true
  }
};

// ❌ Shallow Copy:
const shallowCopy = { ...original };
shallowCopy.settings.theme = "dark";

// Notice that the original object was mutated!
console.log("Original theme after shallow copy mutation:", original.settings.theme);

// ✅ True Deep Copy (Modern JS - ES2022+):
const deepCopy = structuredClone(original);
deepCopy.settings.theme = "high-contrast";

console.log("Original theme after deep copy change:", original.settings.theme);
console.log("Deep copy theme:", deepCopy.settings.theme);
```

**Output:**
```text
Original theme after shallow copy mutation: dark
Original theme after deep copy change: dark
Deep copy theme: high-contrast
```

---

## 7. Quick Summary & Cheat Sheet

| Task | Syntax | Example |
| :--- | :--- | :--- |
| **Deep Access** | `obj.prop1.prop2` | `user.address.city` |
| **Safe Deep Access** | `obj.prop1?.prop2` | `user.address?.city` |
| **Safe Fallback** | `obj.prop1?.prop2 ?? default` | `user.address?.zip ?? "000000"` |
| **Nested Destructure** | `const { a: { b } } = obj` | `const { address: { city } } = user` |
| **Deep Copy** | `structuredClone(obj)` | Creates completely isolated copy |

---

## 8. Common Interview Pitfalls

1. **Destructuring Missing Parent Object:**
   ```javascript
   const data = {};
   // ❌ TypeError: Cannot destructure property 'city' of undefined
   // const { address: { city } } = data;

   // ✅ Safe pattern with default empty object:
   const { address: { city } = {} } = data;
   console.log("City:", city); // undefined (No crash)
   ```
2. **`JSON.parse(JSON.stringify(obj))` limitations:**
   While historically used for deep cloning, it loses functions, `undefined`, `Date` objects, and `NaN`. Always prefer modern `structuredClone(obj)`.
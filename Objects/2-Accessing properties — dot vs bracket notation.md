# 05. Accessing Properties: Dot vs. Bracket Notation

## 1. Overview
JavaScript provides two primary ways to access, modify, or add properties to an object:
1. **Dot Notation (`obj.property`)**
2. **Bracket Notation (`obj["property"]`)**

While **dot notation** is faster to write and easier to read, **bracket notation** provides the dynamic flexibility needed for complex programs.

---

## 2. Quick Syntax Comparison

```javascript
const user = {
  name: "Aarav",
  age: 21
};

// Dot Notation
console.log("Dot Notation:", user.name);

// Bracket Notation (Key must be passed as a string)
console.log("Bracket Notation:", user["name"]);
```

**Output:**
```text
Dot Notation: Aarav
Bracket Notation: Aarav
```

---

## 3. When Dot Notation Fails (Must Use Bracket Notation)

There are **4 critical scenarios** where Dot Notation causes a `SyntaxError` or bugs, and Bracket Notation is mandatory.

---

### A. Dynamic Property Access (Using Variables)
Dot notation treats the word after the dot as a literal property name, while bracket notation evaluates variables and expressions.

```javascript
const car = {
  brand: "Tesla",
  model: "Model 3"
};

const query = "brand";

// Dot notation looks for a literal key named 'query'
console.log("Dot with variable:", car.query);

// Bracket notation evaluates the variable 'query' -> 'brand'
console.log("Bracket with variable:", car[query]);
```

**Output:**
```text
Dot with variable: undefined
Bracket with variable: Tesla
```

---

### B. Keys with Spaces or Special Characters
Keys containing spaces, hyphens (`-`), or symbols cannot be accessed via dot notation because they violate JavaScript identifier naming rules.

```javascript
const order = {
  "order-id": 98452,
  "customer name": "Priya",
  "item#": "A12"
};

// ❌ Dot Notation causes SyntaxError:
// console.log(order.order-id);      // Tries to subtract `id` from `order.order`
// console.log(order.customer name); // SyntaxError

// ✅ Bracket Notation works smoothly:
console.log("Order ID:", order["order-id"]);
console.log("Customer Name:", order["customer name"]);
console.log("Item Code:", order["item#"]);
```

**Output:**
```text
Order ID: 98452
Customer Name: Priya
Item Code: A12
```

---

### C. Keys Starting with or Stored as Numbers
Property names that start with numbers or are purely numeric (like array indices or status codes) require bracket notation.

```javascript
const httpCodes = {
  200: "OK",
  404: "Not Found",
  500: "Internal Server Error"
};

// ❌ SyntaxError with Dot notation:
// console.log(httpCodes.200);

// ✅ Bracket notation:
console.log("200 Code:", httpCodes[200]);   // Auto-coerces number to string "200"
console.log("404 Code:", httpCodes["404"]);
```

**Output:**
```text
200 Code: OK
404 Code: Not Found
```

---

### D. Computed / Evaluated Expressions
You can run any JavaScript expression, math calculation, or string concatenation inside square brackets.

```javascript
const stats = {
  score_1: 85,
  score_2: 92,
  score_3: 78
};

const round = 2;

// Compute the key dynamically
console.log("Round 2 Score:", stats["score_" + round]);
console.log("Math Key Test:", stats["score_" + (1 + 2)]);
```

**Output:**
```text
Round 2 Score: 92
Math Key Test: 78
```

---

## 4. Setting & Modifying Properties

Both notations work for assigning and mutating object properties.

```javascript
const profile = {};

// Setting with dot notation
profile.username = "coder_99";

// Setting with bracket notation (dynamic key)
const dynamicKey = "loginCount";
profile[dynamicKey] = 1;

// Setting key with space
profile["account created"] = "2026-08-15";

console.log(profile);
```

**Output:**
```text
{
  username: 'coder_99',
  loginCount: 1,
  'account created': '2026-08-15'
}
```

---

## 5. Optional Chaining (`?.`) with Both Notations

Optional chaining prevents `TypeError: Cannot read properties of undefined` when accessing nested, possibly non-existent keys.

```javascript
const data = {
  user: {
    name: "Rohan"
  }
};

// Dot notation with optional chaining
console.log("Address City:", data.user?.address?.city);

// Bracket notation with optional chaining
const dynamicField = "zipCode";
console.log("Zip Code:", data.user?.[dynamicField]);
```

**Output:**
```text
Address City: undefined
Zip Code: undefined
```

---

## 6. Summary Comparison Table

| Feature / Scenario | Dot Notation (`obj.key`) | Bracket Notation (`obj["key"]`) |
| :--- | :--- | :--- |
| **Standard identifier** (e.g., `user.age`) | ✅ Yes (Recommended) | ✅ Yes |
| **Dynamic key via variable** | ❌ No (returns `undefined`) | ✅ **Yes (`obj[varName]`)** |
| **Keys with spaces / hyphens** | ❌ SyntaxError | ✅ **Yes (`obj["first name"]`)** |
| **Keys starting with numbers** | ❌ SyntaxError | ✅ **Yes (`obj[100]`)** |
| **Dynamic Expressions / Logic** | ❌ No | ✅ **Yes (`obj["id_" + x]`)** |
| **Readability & Speed of Writing** | ⭐ Higher / Cleaner | Slightly more verbose |

---

## 7. Common Interview Pitfalls

1. **Forgetting Quotes in Bracket Notation:**
   ```javascript
   const obj = { age: 25 };
   // ❌ ReferenceError: age is not defined (looks for variable `age`)
   // console.log(obj[age]); 

   // ✅ Correct:
   console.log(obj["age"]); // 25
   ```

2. **Keys are coerced to Strings:**
   In standard JavaScript objects, all keys inside `[]` are automatically converted to strings:
   ```javascript
   const map = {};
   map[true] = "Boolean Key";
   map[10] = "Numeric Key";

   console.log(map["true"]); // "Boolean Key"
   console.log(map["10"]);   // "Numeric Key"
   ```
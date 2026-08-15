# 09. Object Destructuring (Basic, Renaming & Defaults)

## 1. Overview
Introduced in **ES6 (ECMAScript 2015)**, **Object Destructuring** allows you to unpack properties from an object directly into distinct variables.

> **Key Rule:** Unlike array destructuring (which depends on index position), object destructuring maps properties **strictly by property key name**, regardless of order.

---

## 2. Basic Destructuring

### Before ES6 vs. ES6 Destructuring
```javascript
const user = {
  id: 101,
  username: "rahul_dev",
  email: "rahul@example.com"
};

// ❌ Before ES6 (Repetitive):
// const username = user.username;
// const email = user.email;

// ✅ ES6 Destructuring (Clean):
const { username, email } = user;

console.log("Username:", username);
console.log("Email:", email);
```

**Output:**
```text
Username: rahul_dev
Email: rahul@example.com
```

---

## 3. Renaming Variables (Alias Syntax)

If you want the extracted variable to have a different name than the object's key (to avoid naming collisions or make names clearer), use the colon syntax (`key: newVariableName`).

### Syntax: `{ originalKey: newVariableName }`

```javascript
const response = {
  data_id: "X981",
  u_name: "Sneha"
};

// Rename data_id -> id, and u_name -> userName
const { data_id: id, u_name: userName } = response;

console.log("ID:", id);
console.log("User Name:", userName);

// ⚠️ Note: 'data_id' and 'u_name' are NOT created as variables
// console.log(data_id); // ReferenceError: data_id is not defined
```

**Output:**
```text
ID: X981
User Name: Sneha
```

---

## 4. Default Values

If an extracted property does not exist in the object (or is explicitly `undefined`), you can assign a fallback default value using `=`.

### Syntax: `{ key = defaultValue }`

```javascript
const profile = {
  name: "Tanmay",
  role: "Viewer"
};

// 'role' exists so default is ignored; 'theme' is missing so default applies
const { name, role = "Guest", theme = "dark" } = profile;

console.log("Name:", name);
console.log("Role:", role);
console.log("Theme:", theme);
```

**Output:**
```text
Name: Tanmay
Role: Viewer
Theme: dark
```

---

## 5. Combining Renaming and Default Values

You can rename a property **and** set a default fallback simultaneously.

### Syntax: `{ originalKey: newVariableName = defaultValue }`

```javascript
const settings = {
  auth_token: "xyz_token_123"
};

// Rename 'auth_token' -> 'token'
// Rename 'api_timeout' -> 'timeout' with a default of 5000
const {
  auth_token: token,
  api_timeout: timeout = 5000
} = settings;

console.log("Token:", token);
console.log("Timeout:", timeout);
```

**Output:**
```text
Token: xyz_token_123
Timeout: 5000
```

---

## 6. Destructuring Function Parameters

Passing an object to a function and destructuring its properties directly inside the parameter list makes function signatures clean and self-documenting.

```javascript
// Destructure directly inside arguments with defaults
function displayCard({ title, width = 300, height = 200, isVisible = true }) {
  console.log(`Card: ${title} (${width}x${height}) \vert{} Visible:${isVisible}`);
}

displayCard({
  title: "User Profile",
  width: 450
});
```

**Output:**
```text
Card: User Profile (450x200) | Visible: true
```

---

## 7. Destructuring Already Declared Variables

When destructuring into variables that are already declared with `let`, you **must wrap the assignment in parentheses `(...)`**; otherwise, JavaScript parses the leading `{` as a code block.

```javascript
let title;
let price;

const book = { title: "Clean Code", price: 650 };

// ❌ SyntaxError: Unexpected token '='
// { title, price } = book;

// ✅ Wrap the entire statement in parentheses:
({ title, price } = book);

console.log("Book Title:", title);
console.log("Price: ₹" + price);
```

**Output:**
```text
Book Title: Clean Code
Price: ₹650
```

---

## 8. Rest Operator in Object Destructuring (`...rest`)

Collect all leftover properties into a separate object:

```javascript
const employee = {
  id: 12,
  name: "Deepak",
  department: "DevOps",
  salary: 80000,
  city: "Pune"
};

const { id, name, ...otherDetails } = employee;

console.log("Basic Info:", id, name);
console.log("Other Details:", otherDetails);
```

**Output:**
```text
Basic Info: 12 Deepak
Other Details: { department: 'DevOps', salary: 80000, city: 'Pune' }
```

---

## 9. Quick Cheat Sheet

| Feature | Syntax | Explanation |
| :--- | :--- | :--- |
| **Basic** | `const { a, b } = obj` | Extracts keys `a` and `b` directly |
| **Rename** | `const { a: newA } = obj` | Extracts key `a` into variable `newA` |
| **Default** | `const { a = 10 } = obj` | Fallback if `a` is `undefined` |
| **Rename + Default** | `const { a: newA = 10 } = obj` | Renames and sets default fallback |
| **Rest** | `const { a, ...rest } = obj` | Collects remaining keys into `rest` object |

---

## 10. Common Interview Gotchas

1. **`null` vs `undefined` for Defaults:**
   Default values only trigger on `undefined`. If a property is set to `null`, the default value will **not** take effect (`null` is treated as a defined value).
   ```javascript
   const { x = 10 } = { x: null };
   console.log(x); // null (NOT 10)
   ```
2. **Order Does Not Matter:**
   `const { b, a } = { a: 1, b: 2 };` gives `a = 1` and `b = 2` without issue.
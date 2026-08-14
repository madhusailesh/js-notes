# 01. Array Destructuring in JavaScript

## 1. Overview
Array Destructuring is a feature introduced in **ES6 (ECMAScript 2015)**. It provides a clean, concise syntax to unpack values from arrays into individual variables.

> **Key Rule:** Array destructuring is based on **position (index order)**, not key names.

---

## 2. Before vs. After ES6

### Before ES6 (Manual Indexing)
```javascript
const colors = ["red", "green", "blue"];

const first = colors[0];
const second = colors[1];
const third = colors[2];

console.log(first, second, third); // "red" "green" "blue"
```

### With ES6 Array Destructuring
```javascript
const colors = ["red", "green", "blue"];

// Unpack in a single line
const [first, second, third] = colors;

console.log(first, second, third); // "red" "green" "blue"
```

---

## 3. Core Features & Patterns

### A. Skipping Elements
Use empty commas (`,`) to skip values you do not need.

```javascript
const numbers = [10, 20, 30, 40, 50];

// Extract 1st and 4th items only
const [first, , , fourth] = numbers;

console.log(first);  // 10
console.log(fourth); // 40
```

---

### B. Default Values
If an array element is `undefined` (or the array has fewer elements than variables), default values prevent the variable from becoming `undefined`.

```javascript
const rgb = [255, 128];

// b and a will take default fallback values
const [r, g, b = 0, a = 1] = rgb;

console.log(r); // 255
console.log(g); // 128
console.log(b); // 0 (used default)
console.log(a); // 1 (used default)
```

---

### C. Rest Operator (`...`)
Gathers all remaining elements into a separate new array.

```javascript
const scores = [98, 85, 76, 62, 54];

const [topScore, secondScore, ...remainingScores] = scores;

console.log(topScore);        // 98
console.log(secondScore);     // 85
console.log(remainingScores); // [76, 62, 54]
```

> **Warning:** The rest operator (`...`) **must** always be the last element in the destructuring pattern.

---

### D. Swapping Variables Without a Temp Variable
Destructuring allows swapping variable values in one readable line.

```javascript
let a = 5;
let b = 10;

// Swap values
[a, b] = [b, a];

console.log(a); // 10
console.log(b); // 5
```

---

### E. Nested Array Destructuring
Unpack values from multidimensional (nested) arrays.

```javascript
const nested = [1, [2, 3], 4];

const [first, [innerOne, innerTwo], last] = nested;

console.log(first);    // 1
console.log(innerOne); // 2
console.log(innerTwo); // 3
console.log(last);     // 4
```

---

### F. Destructuring Function Returns
When a function returns an array of values, unpack them immediately.

```javascript
function getCoordinates() {
  return [18.5204, 73.8567]; // [latitude, longitude]
}

const [lat, lng] = getCoordinates();

console.log(lat); // 18.5204
console.log(lng); // 73.8567
```

*(This is the exact design pattern used by React's `useState` hook: `const [state, setState] = useState(initialValue)`).*

---

## 4. Quick Syntax Cheat Sheet

| Feature | Syntax | Output Example |
| :--- | :--- | :--- |
| **Basic** | `const [a, b] = arr` | `[1, 2]` $\rightarrow$ `a = 1, b = 2` |
| **Skip Item** | `const [a, , c] = arr` | `[1, 2, 3]` $\rightarrow$ `a = 1, c = 3` |
| **Default** | `const [a = 10] = []` | `[]` $\rightarrow$ `a = 10` |
| **Rest** | `const [a, ...rest] = arr` | `[1, 2, 3]` $\rightarrow$ `a = 1, rest = [2, 3]` |
| **Swap** | `[a, b] = [b, a]` | Swaps values in-place |

---

## 5. Important Interview Pitfalls

1. **Position Matters, Not Variable Names:** Unlike objects, you can name the variables anything; they map strictly by index order.
2. **Rest Element Syntax Error:** `[...rest, last]` will throw a `SyntaxError: Rest element must be last element`.
3. **Null vs. Undefined Defaults:** Default values only trigger on `undefined`. If an array element is explicitly `null`, the default value will **not** be used (`null` stays `null`).
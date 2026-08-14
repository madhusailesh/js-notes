# 02. Spread and Rest Operators with Arrays

## 1. Overview
Both **Spread** and **Rest** use the exact same three dots syntax (`...`), but they perform **opposite operations** depending on where and how they are used.

* **Spread (`...`)**: **Unpacks / expands** array elements into individual elements. (*"Unpacks a box"*).
* **Rest (`...`)**: **Collects / condenses** multiple individual elements into a single array. (*"Packs things into a box"*).

---

## 2. Spread Operator (`...`)

### A. Copying Arrays (Shallow Copy)
Avoids mutating the original array by reference.

```javascript
const original = [1, 2, 3];

// Creates a new array with the same elements:
const copy = [...original];

copy.push(4);
console.log(original); // [1, 2, 3] (unchanged)
console.log(copy);     // [1, 2, 3, 4]
```

> **Warning:** Spread creates a **shallow copy**. Nested arrays/objects inside the array will still share their references.

---

### B. Combining / Concatenating Arrays
Cleaner alternative to `Array.prototype.concat()`.

```javascript
const fruits = ["apple", "banana"];
const veggies = ["carrot", "spinach"];

// Combine arrays and insert new items anywhere:
const food = ["bread", ...fruits, "rice", ...veggies];

console.log(food);
// ["bread", "apple", "banana", "rice", "carrot", "spinach"]
```

---

### C. Passing Array Elements as Function Arguments
Expands an array into individual arguments for functions that do not accept arrays directly.

```javascript
const numbers = [12, 5, 89, 43, 2];

// Math.max expects numbers: Math.max(n1, n2, n3, ...)
const maxVal = Math.max(...numbers);

console.log(maxVal); // 89
```

---

### D. Converting Iterables to Arrays
Convert strings, sets, or DOM `NodeList` collections into real arrays to use methods like `.map()` or `.filter()`.

```javascript
const word = "HELLO";
const letters = [...word];

console.log(letters); // ["H", "E", "L", "L", "O"]
```

---

## 3. Rest Operator (`...`)

### A. Rest in Array Destructuring
Collects the remaining elements into a new array.

```javascript
const runnerScores = [100, 95, 80, 70, 60];

const [gold, silver, ...others] = runnerScores;

console.log(gold);   // 100
console.log(silver); // 95
console.log(others); // [80, 70, 60]
```

---

### B. Rest in Function Parameters (Variadic Functions)
Collects an indefinite number of passed arguments into a true array, replacing the old `arguments` object.

```javascript
function sum(...numbers) {
  // `numbers` is a real array, so array methods can be used directly
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2));          // 3
console.log(sum(10, 20, 30, 40)); // 100
```

#### Rest with Positional Parameters:
```javascript
function formatTeam(lead, coLead, ...members) {
  console.log(`Lead: ${lead}`);
  console.log(`Co-Lead: ${coLead}`);
  console.log(`Members: ${members.join(", ")}`);
}

formatTeam("Alex", "Sam", "John", "Sara", "Mike");
// Output:
// Lead: Alex
// Co-Lead: Sam
// Members: John, Sara, Mike
```

---

## 4. How to Distinguish Between Spread and Rest

| Aspect | Spread Operator (`...`) | Rest Operator (`...`) |
| :--- | :--- | :--- |
| **Purpose** | Expands / unpacks elements | Gathers / compresses elements |
| **Context** | In array literals `[...]`, function calls `fn(...arr)`, or object literals | In destructuring `[a, ...rest]` or function definitions `fn(...args)` |
| **Position** | Can be placed anywhere (beginning, middle, end) | **Must always be the last element** |

---

## 5. Key Pitfalls & Interview Questions

1. **Rest Element Position:**
   ```javascript
   // ❌ SyntaxError: Rest parameter must be last formal parameter
   const [...others, last] = [1, 2, 3];
   ```

2. **Shallow vs. Deep Copy:**
   ```javascript
   const nested = [[1, 2], [3, 4]];
   const copy = [...nested];

   copy[0].push(99);
   console.log(nested[0]); // [1, 2, 99] -> Inner array reference was shared!
   ```

3. **Rest vs. Old `arguments` Keyword:**
   * `arguments` is an array-like object (no `.map()`, `.reduce()`). It does not exist in arrow functions.
   * `...rest` parameters produce a **true Array instance** and work in all function types.
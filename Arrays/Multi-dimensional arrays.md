# 03. Multi-dimensional Arrays in JavaScript

## 1. Overview
JavaScript does not have native, built-in multi-dimensional array types like C/C++ or Java (e.g., `int[3][3]`). Instead, it creates multi-dimensional arrays using **arrays of arrays** (often called **Jagged Arrays** or **Nested Arrays**).

A 2D array is structured conceptually like a table or a grid with **rows** and **columns**.

```
        Col 0   Col 1   Col 2
Row 0 [   1,      2,      3   ]
Row 1 [   4,      5,      6   ]
Row 2 [   7,      8,      9   ]
```

---

## 2. Creating Multi-dimensional Arrays

### A. Literal Syntax (Static Data)
```javascript
const matrix = [
  [1, 2, 3], // Row 0
  [4, 5, 6], // Row 1
  [7, 8, 9]  // Row 2
];
```

### B. Dynamic Creation (Using `Array.from()` - Recommended)
Creating an empty or pre-filled $R \times C$ (Rows $\times$ Columns) grid safely:

```javascript
const rows = 3;
const cols = 3;

// Create a 3x3 matrix initialized with 0
const grid = Array.from({ length: rows }, () => Array(cols).fill(0));

console.log(grid);
// [ [0, 0, 0], [0, 0, 0], [0, 0, 0] ]
```

> **Warning (The `.fill()` Bug):** 
> Never do `Array(3).fill(Array(3).fill(0))`. This copies the **same array reference** into every row. Modifying `grid[0][0] = 5` would modify all rows simultaneously!

---

## 3. Accessing & Modifying Elements

Use bracket notation in the order `array[rowIndex][columnIndex]`:

```javascript
const board = [
  ["X", "O", "X"],
  ["O", "X", " "],
  [" ", "O", "O"]
];

// Accessing:
console.log(board[0][1]); // "O" (Row 0, Column 1)
console.log(board[1][2]); // " " (Row 1, Column 2)

// Modifying:
board[1][2] = "X";
console.log(board[1][2]); // "X"
```

---

## 4. Iterating Over Multi-dimensional Arrays

### A. Using Nested `for` Loops (Best for Index Control)
```javascript
const matrix = [
  [10, 20],
  [30, 40]
];

for (let i = 0; i < matrix.length; i++) {
  for (let j = 0; j < matrix[i].length; j++) {
    console.log(`Element at [${i}][${j}] =${matrix[i][j]}`);
  }
}
```

### B. Using Nested `forEach` / `for...of` (Best for Readability)
```javascript
const matrix = [
  [10, 20],
  [30, 40]
];

// Using forEach
matrix.forEach((row, rowIndex) => {
  row.forEach((cell, colIndex) => {
    console.log(`Row ${rowIndex}, Col ${colIndex}:${cell}`);
  });
});
```

---

## 5. Adding and Removing Data

Because each row is a normal JavaScript array, you can use standard array methods:

```javascript
const table = [
  [1, 2],
  [3, 4]
];

// 1. Add a new row to the end
table.push([5, 6]);

// 2. Add an element to a specific row
table[0].push(99); 

console.log(table);
// [ [1, 2, 99], [3, 4], [5, 6] ]
```

---

## 6. Flattening Multi-dimensional Arrays

Convert a multi-dimensional array into a 1-dimensional array using ES2019's `.flat()`:

```javascript
const nested2D = [[1, 2], [3, 4], [5, 6]];
const flat1D = nested2D.flat(); 
console.log(flat1D); // [1, 2, 3, 4, 5, 6]

// Deeply nested 3D array (depth parameter)
const nested3D = [1, [2, [3, [4]]]];
console.log(nested3D.flat(2));        // [1, 2, 3, [4]] (flattens 2 levels)
console.log(nested3D.flat(Infinity)); // [1, 2, 3, 4] (flattens completely)
```

---

## 7. Cloning a 2D Array (Deep vs. Shallow Copy)

Because inner rows are references, a standard shallow spread `[...matrix]` is **not enough** to safely copy a 2D array.

```javascript
const matrix = [[1, 2], [3, 4]];

// ❌ Shallow Copy (Mutates original inner rows):
const badCopy = [...matrix];
badCopy[0][0] = 999;
console.log(matrix[0][0]); // 999 (Mutated!)

// ✅ Proper 2D Array Copy (Mapping inner arrays):
const goodCopy = matrix.map(row => [...row]);
goodCopy[0][0] = 42;
console.log(matrix[0][0]); // 999 (Unchanged)

// ✅ Complete Deep Copy (Modern JS):
const modernDeepCopy = structuredClone(matrix);
```

---

## 8. Quick Summary & Interview Tips

| Action | Syntax | Example |
| :--- | :--- | :--- |
| **Access** | `arr[r][c]` | `grid[1][2]` $\rightarrow$ Row 1, Col 2 |
| **Create $R \times C$ Grid** | `Array.from` | `Array.from({length: r}, () => Array(c).fill(0))` |
| **Flatten 2D to 1D** | `.flat()` | `[[1, 2], [3, 4]].flat()` $\rightarrow$ `[1, 2, 3, 4]` |
| **Safe 2D Clone** | `.map()` | `grid.map(row => [...row])` or `structuredClone(grid)` |

* **Interview Gotcha:** Rows do not need to be equal in length. An array like `[[1, 2, 3], [4], [5, 6]]` is completely valid in JavaScript (called a **jagged array**).
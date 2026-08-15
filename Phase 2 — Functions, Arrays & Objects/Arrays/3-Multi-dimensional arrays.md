# 03. Multi-dimensional Arrays in JavaScript

## 1. Overview
JavaScript does not have native multi-dimensional array types like C/C++ or Java (e.g., `int[3][3]`). Instead, it uses **arrays of arrays** (also known as **Nested Arrays** or **Jagged Arrays**).

A 2D array can be visualized like a grid or table with **rows** and **columns**:

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

console.log(matrix);
```
**Output:**
```text
[ [ 1, 2, 3 ], [ 4, 5, 6 ], [ 7, 8, 9 ] ]
```

---

### B. Dynamic Creation using `Array.from()` (Recommended)
Creating an empty or pre-filled $R \times C$ (Rows $\times$ Columns) grid safely:

```javascript
const rows = 3;
const cols = 3;

// Create a 3x3 matrix filled with 0
const grid = Array.from({ length: rows }, () => Array(cols).fill(0));

console.log(grid);
```
**Output:**
```text
[ [ 0, 0, 0 ], [ 0, 0, 0 ], [ 0, 0, 0 ] ]
```

> **Warning (The `.fill()` Reference Bug):** 
> Never use `Array(3).fill(Array(3).fill(0))`. This copies the **same array reference** into every row. Modifying one cell will change every row!

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
console.log("Row 0, Col 1:", board[0][1]);
console.log("Row 1, Col 2:", board[1][2]);

// Modifying:
board[1][2] = "X";
console.log("After update Row 1, Col 2:", board[1][2]);
```
**Output:**
```text
Row 0, Col 1: O
Row 1, Col 2:  
After update Row 1, Col 2: X
```

---

## 4. Iterating Over Multi-dimensional Arrays

### A. Using Nested `for` Loops
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
**Output:**
```text
Element at [0][0] = 10
Element at [0][1] = 20
Element at [1][0] = 30
Element at [1][1] = 40
```

---

### B. Using Nested `forEach`
```javascript
const matrix = [
  ["A", "B"],
  ["C", "D"]
];

matrix.forEach((row, rowIndex) => {
  row.forEach((cell, colIndex) => {
    console.log(`Row ${rowIndex}, Col ${colIndex}:${cell}`);
  });
});
```
**Output:**
```text
Row 0, Col 0: A
Row 0, Col 1: B
Row 1, Col 0: C
Row 1, Col 1: D
```

---

## 5. Adding Data to 2D Arrays

```javascript
const table = [
  [1, 2],
  [3, 4]
];

// 1. Add a new row to the end
table.push([5, 6]);

// 2. Add an element to the first row (Row 0)
table[0].push(99);

console.log(table);
```
**Output:**
```text
[ [ 1, 2, 99 ], [ 3, 4 ], [ 5, 6 ] ]
```

---

## 6. Flattening Multi-dimensional Arrays (`.flat()`)

Convert a nested array into a 1D array:

```javascript
// 2D Array
const nested2D = [[1, 2], [3, 4], [5, 6]];
console.log("Flatten 2D:", nested2D.flat());

// Deeply nested 3D/4D Array
const nestedDeep = [1, [2, [3, [4]]]];
console.log("Flatten 2 levels:", nestedDeep.flat(2));
console.log("Flatten completely:", nestedDeep.flat(Infinity));
```
**Output:**
```text
Flatten 2D: [ 1, 2, 3, 4, 5, 6 ]
Flatten 2 levels: [ 1, 2, 3, [ 4 ] ]
Flatten completely: [ 1, 2, 3, 4 ]
```

---

## 7. Cloning a 2D Array (Shallow vs. Deep Copy)

```javascript
const original = [[1, 2], [3, 4]];

// ❌ Bad (Shallow copy shares inner row references)
const shallow = [...original];
shallow[0][0] = 999;
console.log("Original after shallow change:", original[0][0]);

// ✅ Good (Deep copy using structuredClone or .map)
const deep = structuredClone(original);
deep[0][0] = 42;
console.log("Original after deep change:", original[0][0]);
console.log("Deep copy value:", deep[0][0]);
```
**Output:**
```text
Original after shallow change: 999
Original after deep change: 999
Deep copy value: 42
```

---

## 8. Quick Cheat Sheet

| Operation | Code Syntax | Example Output |
| :--- | :--- | :--- |
| **Access** | `arr[r][c]` | `grid[0][1]` $\rightarrow$ Element at Row 0, Col 1 |
| **Modify** | `arr[r][c] = val` | `grid[0][0] = 5` |
| **Flatten** | `arr.flat()` | `[[1, 2], [3]].flat()` $\rightarrow$ `[1, 2, 3]` |
| **Deep Copy** | `structuredClone(arr)` | Independent duplicate of entire grid |
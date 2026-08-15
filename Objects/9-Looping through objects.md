# 11. Looping Through Objects in JavaScript

## 1. Overview
Plain JavaScript objects are **not directly iterable** using a simple `for...of` loop (e.g., `for (const x of obj)` throws `TypeError: obj is not iterable`). 

To loop through the keys, values, or entries of an object, JavaScript provides **4 primary methods**:

```
                       Ways to Loop an Object
                                │
        ┌───────────────────────┼────────────────────────┐
        ▼                       ▼                        ▼
  for...in loop          Object.keys()            Object.entries()
(Iterates keys +        (Returns array           (Returns [k, v] pairs;
 prototype chain)          of keys)             cleanest modern syntax)
```

---

## 2. Method 1: `for...in` Loop

The `for...in` loop iterates over all **enumerable property keys** of an object.

```javascript
const user = {
  name: "Ishaan",
  age: 23,
  role: "Frontend Engineer"
};

for (const key in user) {
  // Use bracket notation to access values dynamically
  console.log(`${key}:${user[key]}`);
}
```

**Output:**
```text
name: Ishaan
age: 23
role: Frontend Engineer
```

### ⚠️ The Prototype Trap with `for...in`
`for...in` also iterates over inherited properties up the prototype chain. Always filter with `Object.hasOwn()` or `hasOwnProperty()` when using `for...in`:

```javascript
const parent = { inheritedProp: "from prototype" };
const child = Object.create(parent);
child.ownProp = "from child";

// Without check: loops over inherited properties too
for (const key in child) {
  if (Object.hasOwn(child, key)) {
    console.log("Own Property Only:", key, "->", child[key]);
  }
}
```

**Output:**
```text
Own Property Only: ownProp -> from child
```

---

## 3. Method 2: `Object.keys()` with `forEach` or `for...of`

`Object.keys(obj)` returns an array containing only the object's **own** keys.

```javascript
const laptop = {
  brand: "Apple",
  chip: "M3 Pro",
  ram: "18GB"
};

// 1. Using for...of
for (const key of Object.keys(laptop)) {
  console.log(`Key: ${key} \vert{} Value:${laptop[key]}`);
}

// 2. Using .forEach()
Object.keys(laptop).forEach((key) => {
  console.log(`forEach -> ${key} =${laptop[key]}`);
});
```

**Output:**
```text
Key: brand | Value: Apple
Key: chip | Value: M3 Pro
Key: ram | Value: 18GB
forEach -> brand = Apple
forEach -> chip = M3 Pro
forEach -> ram = 18GB
```

---

## 4. Method 3: `Object.values()` (Iterating Only Values)

If you only need the property values and don't care about the key names, use `Object.values(obj)`.

```javascript
const monthlySales = {
  jan: 12000,
  feb: 15000,
  mar: 18500
};

// Calculate total sales directly
let total = 0;
for (const amount of Object.values(monthlySales)) {
  total += amount;
}

console.log("Total Sales: ₹" + total);
```

**Output:**
```text
Total Sales: ₹45500
```

---

## 5. Method 4: `Object.entries()` (⭐ Recommended Modern Standard)

`Object.entries(obj)` converts the object into an array of `[key, value]` pairs, allowing you to use array destructuring cleanly inside a `for...of` loop.

```javascript
const course = {
  title: "Full Stack JavaScript",
  duration: "6 Months",
  price: 4999
};

for (const [key, value] of Object.entries(course)) {
  console.log(`${key.toUpperCase()} ->${value}`);
}
```

**Output:**
```text
TITLE -> Full Stack JavaScript
DURATION -> 6 Months
PRICE -> 4999
```

---

## 6. Functional Transformations with `Object.entries()`

Using `Object.entries()` combined with `.map()`, `.filter()`, or `.reduce()` enables clean, functional object transformations.

```javascript
const productPrices = {
  notebook: 80,
  pen: 20,
  bag: 450,
  eraser: 10
};

// Filter items with price > 50 and apply a 10% discount:
const discountedItems = Object.entries(productPrices)
  .filter(([item, price]) => price > 50)
  .map(([item, price]) => [item, price * 0.9]);

// Convert entries back into an object
const finalOffer = Object.fromEntries(discountedItems);

console.log("Filtered & Discounted:", finalOffer);
```

**Output:**
```text
Filtered & Discounted: { notebook: 72, bag: 405 }
```

---

## 7. Comparison Summary Table

| Method | Access | Iterates Inherited Props? | Best Use Case |
| :--- | :--- | :--- | :--- |
| **`for...in`** | Keys only | **Yes** (Requires `hasOwn` check) | Legacy code / Quick scans |
| **`Object.keys()`** | Keys only | **No** (Own properties only) | When you only need property names |
| **`Object.values()`** | Values only | **No** (Own properties only) | Aggregations (sum, average, max) |
| **`Object.entries()`** | Keys & Values | **No** (Own properties only) | **Default choice** for modern JS |

---

## 8. Common Interview Pitfalls

1. **Direct `for...of` on Objects:**
   ```javascript
   const obj = { a: 1, b: 2 };
   // ❌ TypeError: obj is not iterable
   // for (const x of obj) {}
   
   // ✅ Correct:
   for (const [k, v] of Object.entries(obj)) {}
   ```

2. **Property Order in Objects:**
   JavaScript iterates object keys in a deterministic order:
   - **Integer keys** in ascending numerical order (`1`, `2`, `10`).
   - **String keys** in order of insertion.
   - **Symbols** in order of insertion.
# 12. How JavaScript Works Under the Hood

## 1. Core Architecture Overview
JavaScript is defined by three fundamental characteristics:
1. **Single-Threaded:** It has only **one call stack** and executes **one piece of code at a time**.
2. **Synchronous by Default:** Code is executed line by line in the exact order it appears.
3. **JIT-Compiled (Just-In-Time):** Modern engines do not purely interpret line-by-line; they compile code into optimized machine code right before execution.

```
+-------------------------------------------------------------+
|                      JavaScript Runtime                     |
|                                                             |
|   +-----------------------+     +-----------------------+   |
|   |      Call Stack       |     |      Memory Heap      |   |
|   |  (Code execution &    |     |  (Memory allocation   |   |
|   |   Context tracking)   |     |   for objects/vars)   |   |
|   +-----------------------+     +-----------------------+   |
|                                                             |
+-------------------------------------------------------------+
```

---

## 2. What Does "Single-Threaded & Synchronous" Mean?

Because JavaScript has only one main thread, long-running tasks can block the entire program (known as **blocking the thread**).

### Blocking Code Example:
```javascript
console.log("Start");

// Simulating a heavy, blocking calculation
function blockThread(milliseconds) {
  const start = Date.now();
  while (Date.now() - start < milliseconds) {
    // Thread is busy doing nothing else
  }
}

blockThread(1000); // Blocks for 1 second

console.log("End");
```

**Output:**
```text
Start
(waits exactly 1 second...)
End
```

> **How does JS handle Async tasks?** 
> JavaScript delegates asynchronous operations (like `setTimeout`, `fetch`, DOM Events) to the **Host Environment (Browser APIs or Node.js C++ APIs)**, using the **Event Loop** to push results back to the single thread when ready.

---

## 3. Interpreted vs. Compiled vs. JIT (Just-In-Time)

Modern JavaScript engines combine the fast startup of an **Interpreter** with the high performance of a **Compiler**.

```
 Source Code (.js)
        │
        ▼
     Parser  ──►  AST (Abstract Syntax Tree)
        │
        ▼
   Interpreter (Ignition)  ──►  Executes Bytecode immediately (Fast start)
        │
        ▼ (Monitors "Hot Code" running repeatedly)
 Optimizing Compiler (TurboFan)  ──► Highly Optimized Machine Code (Fast execution)
```

1. **Parser & AST:** The engine parses `.js` text into an **Abstract Syntax Tree (AST)**.
2. **Interpreter (e.g., Ignition in V8):** Quickly turns AST into bytecode and starts execution immediately without waiting to compile the whole file.
3. **Profiler (Monitor):** Watches the running code to identify "hot functions" (code executed many times with identical data types).
4. **Optimizing Compiler (e.g., TurboFan in V8):** Compiles hot functions directly into ultra-fast machine code.
5. **Deoptimization:** If assumptions fail (e.g., passing a string into a function that previously only accepted numbers), the engine deoptimizes back to bytecode.

---

## 4. Major JavaScript Engines

Every browser and runtime embeds an engine responsible for parsing and running JavaScript:

| Engine Name | Developed By | Used In | Key Features |
| :--- | :--- | :--- | :--- |
| **V8** | Google | Google Chrome, Node.js, Deno, Brave | Uses *Ignition* (Interpreter) + *TurboFan* (Compiler) |
| **SpiderMonkey** | Mozilla | Firefox | The first JS engine ever created (by Brendan Eich in 1995) |
| **JavaScriptCore (Nitro)** | Apple | Safari, Bun runtime | Uses a multi-tiered compilation pipeline |

---

## 5. Summary Cheat Sheet

| Property | What It Means |
| :--- | :--- |
| **Single-Threaded** | 1 Call Stack = executes only 1 statement at any given millisecond. |
| **Synchronous** | Runs in sequential order from top to bottom unless offloaded to Web APIs. |
| **JIT Compilation** | Combines fast initial interpretation with runtime machine-code optimization. |
| **V8 Engine** | The C++ engine powering Chrome and Node.js. |
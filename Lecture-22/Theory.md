# EVENT LOOP PRACTICAL

- Synchronous code executes immediately before asynchronous code.

- In Micro-task queue, the `nextTick` callbacks will be executed first before any other callbacks.

#### main.js
```javascript
console.log("First"); // Synchronous Code

process.nextTick(() => console.log("nextTick callback called")); // Asynchronous Code - Next Tick (Micro Task Queue)

Promise.resolve("success").then((response) => console.log("Promise callback called", response)) // Asynchronous Code - Promise (Micro Task Queue)

setTimeout(() => console.log("Timeout callback called"), 0); // Asynchronous Code - Timer (Macro Task Queue)

setImmediate(() => console.log("Immediate callback called")); // Asynchronous Code - Check (Macro Task Queue)
 
console.log("Third"); // Synchronous Code

/*
The output will be:
First
Third
nextTick callback called       
Promise callback called success
Timeout callback called        
Immediate callback called
*/
```

> Note: The `priority` sequence of code execution applies only when all callbacks are ready at the exact same time. Otherwise, if a higher-priority callback takes slightly longer to complete, it will be executed later accordingly. 

#### main.js
```javascript
const fs = require("fs");

console.log("First"); // Synchronous Code

process.nextTick(() => console.log("nextTick callback called")); // Asynchronous Code - Next Tick (Micro Task Queue)

Promise.resolve("success").then((response) => console.log("Promise callback called", response)) // Asynchronous Code - Promise (Micro Task Queue)

setTimeout(() => console.log("Timeout callback called"), 0); // Asynchronous Code - Timer (Macro Task Queue)

fs.readFile("data.txt", "utf-8", () => console.log("Read file callback called")); // Asynchronous Code - I/O (Macro Task Queue)

setImmediate(() => console.log("Immediate callback called")); // Asynchronous Code - Check (Macro Task Queue)
 
console.log("Third"); // Synchronous Code

/*
The output will be:
First
Third
nextTick callback called       
Promise callback called success
Timeout callback called        
Immediate callback called      
Read file callback called 
*/
```

- In the above example, `fs.readFile` takes some time to read the file, so it gets executed last.
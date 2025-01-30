# BACKGROUND

- The term `I/O` usually refers to `input/output`.

- In Node JS, the term `I/0` often refers to reading/writing files or network operations.

- Network operations get `external information` into your application, or send data from your application to somewhere else.

### Thread Pool

- Libuv's thread pool helps execute some of the async methods but, not all async methods.

- Although both `crypto.pbkdf2()` and `https.request()` are asynchronous, but `https.request()` method does not seem to use the thread pool.

- `https.request()` does not seem to be affected by the number of CPU cores either.

#### Syntax
```javascript
// Import HTTPS Module
const https = require("https");

// Initialize Starting Time
const start = Date.now();

// Run Multiple Requests At The Same Time
for(let i = 0; i <= 10; i++)
{
    https.request("https://www.google.com.pk/", (response) => {
        response.on("data", () => {});
    
        response.on("end", () => {
            console.log(`Request ${i + 1} Total Time Consumed ${Date.now() - start}`); // Track the total time consumed
        });
    }).end(); // End request
}
```

### NETWORK I/O

- `https.request` is a network `input/output` operation and not a CPU bound operation.

- It does not use the thread pool.

- Libuv passed the work to the operating system `kernel` and whenever possible, it will check the kernel and see if the request has completed.

### Kernel In Operating System

- A kernel is a `core component` of an operating system that manages the operations of computers and hardware.

- It basically manages operations of memory and CPU time.

- It acts as a bridge between `software applications` and the `hardware` of a computer.

- The kernel manages system resources, such as the CPU, memory, and devices, ensuring everything works together smoothly and efficiently.

- It handles tasks like running programs, accessing files, and connecting to devices like printers and keyboards.

- In Node JS, async methods are handled by Libuv, but are handled in 2 different ways.
1. Native Async Mechanism
2. Thread Pool

- Whenever possible, Libuv will use native async mechanisms in the OS so as avoid blocking the main thread.

- Relying on `Native Async Mechanisms` makes `Node.js` scalable as the only limitation is the `operating system kernel`.
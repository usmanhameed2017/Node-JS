# EVENT LOOP IN NODE JS

### Code Execution In Node JS

- `V8 Engine` process the Node.js code and execute it.

- `V8 Engine` consist of two memories:
1. Memory Heap
2. Call Stack

#### Memory Heap

- All declared variables and functions resides in `Memory Heap.`

#### Call Stack

- Whenever we execute the code, functions are pushed into the `Call Stack` memory.

- Call stack basically provides the code execution environment.

- The sequence of code execution in `Call Stack` is `LIFO (Last In First Out)`.

### Async Code Execution In Node JS

- JavaScript is a `synchronous`, `blocking`, `single-threaded` by default, meaning it executes the code line by line, blocking further execution until the current operation completes.

- However, JavaScript also supports `asynchronous` behavior using mechanisms like: `Callbacks`, `Promises`, `async & await`.

- To make `async` programming possible in `Node.js`, we need the help of `Libuv`.

- Whenever an `asynchronous` function pushed into the `Call Stack` memory, `Libuv` have two ways to handle that function.
1. Native Async Mechanism
2. Thread Pool

### Synchronous Code Execution

- In `Call Stack` memory, the `global()` function is the first one to be executed.

- `Global` function refers to the global scope.

- All `callbacks` assigned to the `Thread Pool` are registered in the event loop, which then determines their priority and pushes them onto the call stack memory.

#### Event Loop Phases
1. Timers - setTimeout(), setInterval()   
2. Pending Callbacks - This phase executes callbacks for some system operations such as types of TCP errors.
3. Idle, Prepare - Used Internally
4. Poll - I/O Events (FS) Read File
5. Check - setImmediate()
6. Close Callbacks - Socket Close

### Event Loop - Execution Order

- Any callbacks in the `micro task queues` are executed. First, `nextTick queue` and after that `promise queue`.

- Then all callbacks within the `timer queue` are executed.

- Any callbacks in the `micro task queues` (if present) are executed. First, `nextTick queue` and after that `promise queue`.

- All callbacks within the 1/0 queue (Poll) are executed.

- Any callbacks in the `micro task queues` (if present) are executed. First, `nextTick queue` and after that `promise queue`.

- All callbacks in the `check queue` are executed.

- Any callbacks in the `micro task queues` (if present) are executed. First, `nextTick queue` and after that `promise queue`.

- All callbacks in the close queue are executed.

- For one final time in the same loop, the `micro task queue`s are executed. `nextTick queue` followed by `promise queue`.

> Note: `Timers` & `Poll` are `Macro Task Queues`.

#### Micro-Tasks

```
small
high-priority
executed before any other tasks
Promises
MutationObserver
queueMicrotask
process.nextTick in Node.js
```

#### Macro-Tasks
```
larger
lower-priority
executed after all micro-tasks
setTimeout
setlnterval
UI rendering
```

### Event Queue

- The order sequence of execution is always set inside `Event Queue`.

- The order sequence in `Event Queue` is `FIFI (First In First Out)`.

### Important Notes

- User written synchronous JavaScript code takes priority over `async` code that the runtime would like to execute.

- Only after the `Call Stack` is empty, the event loop comes into the picture.

- If there are more callbacks to be processed, the event loop is kept alive for one more run and the same steps are repeated.

- On the other hand, if all callbacks are executed and there is no more code to process, the event loop exits.

- Callback functions are executed only when the call stack is empty. So that the flow of `synchronous` code execution will not be interrupted to run a callback function.

- In `Event Loop`, `setTimeout()` and `setlnterval()` callbacks are given first priority.

- Timer callbacks are executed before I/O callbacks even if both are ready at the exact same time.

- An event loop is an `endless loop`, which waits for tasks, executes them, and then sleeps until it receives more callbacks.

- The event loop allows programs to remain responsive while handling potentially long-running tasks asynchronously.

- The event loop is a `fundamental mechanism` that enables the asynchronous execution of code.
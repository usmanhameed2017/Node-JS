# THREAD POOL IN NODE JS

- When multiple clients send requests to a `Node.js` server at the same time, the main thread receives them and assigns the requests to the `thread pool`.

- The `Thread Pool` consists of multiple threads. By default, it has four threads, but the number can be increased using an environment variable.

- When the threads in a `Thread Pool` complete their assigned tasks, they return the response via a `callback` function.

- The concept of a thread pool is provided by the `libuv` library, which was initially built for Node.js.

- `Libuv` is built in `C Language`.

- `C language` uses `System Kernel`.

- System Kernel has `Multiple Threads`.

- It means in Node JS, we are not using multiple threads but behind the scenes `Libuv's thread pool` is implementing multiple threads.

- Libuv provides you the concept of Non-Blocking I/O.

- Libuv handles asynchronous non-blocking operations in Node JS.

- Every method in Node JS that has the `Sync` suffix always runs on the main thread and is blocking.

- A few `async` methods like `fs.readFiIe()` and `crypto.pbkdf2()` run on a separate in `Libuv's thread pool`. They do run synchronously in their own thread but as far as the main thread is concerned, it appears as the method is running asynchronously.
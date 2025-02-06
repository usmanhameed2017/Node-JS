# IMPORTANT POINTS
- When we write code in JavaScript, it converts into a machine language, then our machine understands the code of JavaScript.

- The `JavaScript Engine` is used to convert JS code into a machine language.

- The JavaScript Engine is basically a medium between Javascript code and a machine, which transforms the code into a machine readable language.

- The first JavaScript Engine called `SpiderMonkey` was created by Netscape.

- Netscape introduced this SpiderMonkey engine in their web browser called `Netscape Navigator`.

- Google introduced the fastest JavaScript engine called the `V8 engine` for their Chrome browser.

- JavaScript v8 engine is written in C++.

- Node.js is an open-source and cross-platform `JavaScript Runtime Environment`.

- Node.js was developed by `Ryan Dahl` in 2009.

- Node JS is associated with `Walmart`, `PayPal`, `Netflix`, `Uber`, `LinkedIn`, `ebay` etc.

- When your application starts getting larger, maintaining a single file becomes a difficult task.

- It is easy to get lost in the codebase and lose track of what a particular piece of code is doing.

- To make it easier to maintain, reuse and organize code, you need to split the code into multiple files.

- This process is called `modularization`.

- There are three types of modules, including; `Local Modules`, `Core Modules`, `Third-Party Modules (External Modules)`.

- Local module is a file with `js` extension, having some chunk of javascript code.

- Core modules are come with the Node.js by default.

- Third-party modules are downloaded with a package manager such as `npm`.

- Initialize Node.js project with the command: `npm init`.

- `package.json` file is primarily used for managing and documenting `metadata` about the project, including its `name`, `version`, `author`, `dependencies`, `scripts`, and other configuration details within json format.

- `package-lock.json` file is generated and updated automatically by npm when installing or updating packages. It is used to lock the exact versions of dependencies installed in the project, ensuring reproducibility and consistent installations across different environments.

- Node.js version `18.11` brings a new feature called `--watch` that allows hot reloading without relying on nodemon.

- `Synchronous` is a sequential & blocking architecture, so the execution of each operation depends on completing the one before it.

- `Sync` is a single-thread, so only one operation or program will run at a time.

- `Sync` is a blocking model and it will only send the server one request at a time and wait for that request to be answered by the server.

- `Asynchronous` is a non-blocking architecture, so the execution of one task isn't dependent on another.

- `Async` is a multi-thread, which means operations or programs can run in parallel.

- `Async` is non-blocking, which means it will send multiple requests to a server.

- Node JS follows `Asynchronous`, but Node JS also have `synchronous methods` to perform synchronous specific tasks.

- Fs module is used to provide `file handling`.

- File handling refers to the process by which data is stored and retrieved from `files` using a program.

- Whether you need to read configuration files, write `log files`, the fs module provides the necessary tools to interact with the file system efficiently.

- `copyFileSync` is limited to copying files; does not support copying directories.

- `cpSync` copies files and directories recursively and is suitable for copying both individual files and entire directories.

- Node js web server works as a `single-thread`, but its single thread will not be blocked.

- It means the single thread of Node.js will not be blocked, nor it stay in pending state.

- To handle multiple request with single thread, Node.js have two concepts:
1. Non-Blocking I/O
2. Asynchronous

- There is a library called `libuv` specifically designed for Node.js. 

- Libuv provides you the concept of Non-Blocking I/O.

- Libuv is built in `C Language`.

- C language uses `System Kernel`.

- System Kernel has `Multiple Threads`.

- It means in Node JS, we are not using multiple threads but behind the scenes Kernel is implementing multiple threads.

- When a client sends a request to a server, the server listens for it on a specific port number.

- However, ensure that the port number is not already in use by another application.

- Node JS applications often listen on port 3000 or 4000, but those are just conventions, and you don't have to follow them.

- When a client sends a request to a server, it is referred to as an `HTTP Request Header`. 

- When the server processes the request and sends back a response, it is known as an `HTTP Response Header`.

- Node JS has a built-in module called `HTTP`, which allows Node JS to transfer data over the Hyper Text Transfer Protocol (HTTP).

- The HTTP module allows you to create a server using the `http.createServer()` method, which listens for incoming requests and handles them using a `callback` function.

- A hash sign `#` in a URL is referred to as a fragment.

- Historically, URL fragments have been used to automatically set the browser's scroll position to a predefined location in the web page.

- In that sense, if a URL refers to a `document`, then the `fragment` refers to a `specific subsection` of that document.

- The URL module splits up a web address into a readable parts.

- The Node JS `URL module` provides us with utilities for parsing of the URL.

- `Parsing` is the process of transforming an unstructured data into a tree or parse tree (structured data) that's easier to read, understand and use.

###### https://www.example.com/category/shirts?name=Usman&age=24#section7

- The segments of the above URL are:
>1. `https`: Protocol 
>2. `www.example.com`: Domain/Host name
>3. `category`: Path 
>4. `category/shirts`: Nested Path 
>5. `?`: Query String Separator
>6. `?name=Usman&age=24`: Query String Parameters 
>7. `#section7`: Fragment 

- The path module in Node JS is a core module that offers utilities for managing file and directory paths.

- It helps handle and transform paths across different operating systems, ensuring platform independence.

- In Node.js, when we create a module and add code to it, Node.js automatically wraps the entire file's code in a `Module Wrapper Function` behind the scenes.

- The `Module Wrapper Function` creates a `block scope`, ensuring that variables or functions with the same name do not conflict.

- Additionally, the Module Wrapper Function provides some seemingly global variables as its parameters, including `exports`, `require`, `module`, `__filename`, and `__dirname`. These variables are accessible in every file and can be used as needed based on your work requirements.

- Behind the scenes, Node.js does not run our code directly, it wraps the entire code inside a `Module Wrapper Function` before execution. This is why, in every file, you have access to seemingly global variables like `exports`, `require`, `module`, `__filename`, and `__dirname`.

- The `Module Wrapper Function` provides a proper encapsulation and code reusability.

- JSON is a standard text-based and human readable data interchange format used to exchange data between `web clients` and `web servers`.

- JSON is a collection of `key-value` pairs where the key must be a string and the value can be any of following types: Number, String, Boolean, Array, Object and NULL.

- `JSON.parse()` takes a JSON string and converts it into a JavaScript object.

- `JSON.stringify()` converts a JavaScript object into a JSON string.

- `require` method automatically converts JSON string into a Javascript object. So, you don't need to use `JSON.parse()` method for it.

- A callback is a function passed as an argument to another function.

- This technique allows a function to call another function.

- A callback function which is executed immediately is called a `Synchronous Callback`.

- A callback function that is often used to continue code execution after an Asynchronous operation has completed in a High Order Function is called `Asynchronous Callback`.

- `Asynchronous Callbacks` are used to delay the execution of a function until a particular event has occurred such as; reading file, fetching data from a server, updating a data etc.

- An event is an `action` that has happened in our application we can respond to.

- By using the events module, we can create our own custom events and respond to those custom events in a `non-blocking` manner.

- Node.js has a built-in module, called `events`, where you can create, fire, and listen for your own events.

- We can attach multiple `Event Handlers` to a single `Event`.

- When multiple clients send requests to a `Node.js` server at the same time, the main thread receives them and assigns the requests to the `thread pool`.

- The `Thread Pool` consists of multiple threads. By default, it has four threads, but the number can be increased using an environment variable.

- Increase thread pool size: `process.env.UV_THREADPOOL_SIZE = 8;`

- When the threads in a `Thread Pool` complete their assigned tasks, they return the response via a `callback` function.

- The concept of a thread pool is provided by the `libuv` library, which was initially built for Node.js.

- It means in Node JS, we are not using multiple threads but behind the scenes `Libuv's thread pool` is implementing multiple threads.

- Libuv provides you the concept of Non-Blocking I/O.

- Libuv handles asynchronous non-blocking operations in Node JS.

- Every method in Node JS that has the `Sync` suffix always runs on the main thread and is blocking.

- A few `async` methods like `fs.readFiIe()` and `crypto.pbkdf2()` run on a separate in `Libuv's thread pool`. They do run synchronously in their own thread but as far as the main thread is concerned, it appears as the method is running asynchronously.

- By increasing the thread pool size, we are able to improve the total time taken to run multiple calls of an asynchronous method like `pbkdf2()` for hashing.

- Increasing the thread pool size can help with performance but that is limited by the number of available CPU cores.

- The total number of threads depends on the `CPU cores`. We cannot just increase the thread pool size by assigning some extremely high number. If our system has 8 CPU cores, we can increase the thread pool size up to 8 or beyond but, then the threads will be divided among 8 CPU cores.

- In Node JS, the term `I/0` often refers to reading/writing files or network operations.

- Network operations get `external information` into your application, or send data from your application to somewhere else.

- Libuv's thread pool helps execute some of the async methods but, not all async methods.

- Although both `fs.readFile()` and `https.request()` are asynchronous, but `https.request()` method does not seem to use the thread pool.

- `https.request()` does not seem to be affected by the number of CPU cores either.

- `https.request` is a network `input/output` operation and not a CPU bound operation.

- A kernel is a `core component` of an operating system that manages the operations of computers and hardware.

- It basically manages operations of memory and CPU time.

- It acts as a bridge between `software applications` and the `hardware` of a computer.

- In Node JS, async methods are handled by Libuv, but are handled in 2 different ways.
1. Native Async Mechanism
2. Thread Pool

- Whenever possible, Libuv will use native async mechanisms in the OS so as avoid blocking the main thread.

- Relying on `Native Async Mechanisms` makes `Node.js` scalable as the only limitation is the `operating system kernel`.

- `V8 Engine` consist of two memories:
1. Memory Heap
2. Call Stack

- All declared variables and functions resides in `Memory Heap`.

- Whenever we execute the code, functions are pushed into the `Call Stack` memory.

- Call stack basically provides the code execution environment.

- The sequence of code execution in `Call Stack` is `LIFO (Last In First Out)`.

- JavaScript is a `synchronous`, `blocking`, `single-threaded` by default, meaning it executes the code line by line, blocking further execution until the current operation completes.

- However, JavaScript also supports `asynchronous` behavior using mechanisms like: `Callbacks`, `Promises`, `async & await`.

- To make `async` programming possible in `Node.js`, we need the help of `Libuv`.

- Whenever an `asynchronous` function pushed into the `Call Stack` memory, `Libuv` have two ways to handle that function.
1. Native Async Mechanism
2. Thread Pool

- In `Call Stack` memory, the `global()` function is the first one to be executed.

- `Global` function refers to the global scope.

#### Event Loop Phases
1. Timers - setTimeout(), setInterval()   
2. Pending Callbacks - This phase executes callbacks for some system operations such as types of TCP errors.
3. Idle, Prepare - Used Internally
4. Poll - I/O Events (FS) Read File
5. Check - setImmediate()
6. Close Callbacks - Socket Close

- The order sequence of execution is always set inside `Event Queue`.

- The order sequence in `Event Queue` is `FIFO (First In First Out)`.

- Callback functions are executed only when the call stack is empty. So that the flow of `synchronous` code execution will not be interrupted to run a callback function.

- An event loop is an `endless loop`, which waits for tasks, executes them, and then sleeps until it receives more callbacks.

- The event loop is a `fundamental mechanism` that enables the asynchronous execution of code.

> Note: The `priority` sequence of code execution applies only when all callbacks are ready at the exact same time. Otherwise, if a higher-priority callback takes slightly longer to complete, it will be executed later accordingly. 

- `app.use()` is used to define middleware that executes on every request, regardless of the HTTP method (GET, POST, PUT, DELETE).

- `express.static()` middleware serves static files like HTML, CSS, JavaScript, and images from a specified directory on the server.

- `express.static()` automates the process of `serving static files` by mapping URLs directly to files in the specified directory, simplifying configuration and reducing development effort.

- When a client requests a static file, Express.js uses `express.static` to locate and deliver the file directly to the client's browser without additional routing logic.

- EJS (`Embedded JavaScript`) is a popular template engine for Node JS.

- A template engine in web development is a tool that helps you create `dynamic HTML pages`.

- EJS `Embedded JavaScript` is a popular template engine for Node.js that allows you to generate HTML markup with `plain JavaScript`.

- It is specifically useful for creating `dynamic web pages`, as it enables you to embedded JavaScript logic directly within your HTML.

- Template engine helps to create an HTML template with minimal code. Also, it can `inject data` into an HTML template on the client side and produce the final HTML.

- To write your Javascript expression inside HTML, use EJS placeholder syntax like this: `<% %>` and to print any value, use EJS placeholder with `is equal` sign like this: `<%= name %>`.

- To share and include common code in multiple files, you need to use Hyphen `-` signature with `%` followed by `include()` method.

- Syntax: <%- include('filename') %>

- `Middleware` is a request handler that allows you to intercept and manipulate requests and responses before they reach `route handlers`.

- `Middleware` is just like a `handler function` that take three parameters:
1. `Request object` to perform manipulation with client request.
2. `Response object` to perform manipulation with response and to add custom properties inside response object.
3. `Next() method` to forward the client request to another middleware or route.

- `Middleware` executes right before routing, meaning it receives the client request before it reaches the intended route.

- Middleware functions can perform the following tasks: 
1. Execute any code.
2. Make changes to the request and the response objects.
3. End the request-response cycle.
4. Call the next middleware function in the stack.

- `Middleware` is a flexible tool that helps add functionalities like `logging`, `authentication`, `error handling`, and more to Express applications.

- If the current middleware function does not end the request-response cycle, it must call `next()` to pass control to the next middleware function. Otherwise, the request will be left hanging.

- Operational errors are the problems that we can predict that will happen at some point in future. We need to handle them in advance.

- Programming errors are simply bugs that we programmers, by mistake, introduces them in our code.

- In Express.js, `error-handling middleware` is a function designed to catch and manage errors occurring in your `routes` or other `middleware`.

- When we pass an argument to the `next()` method, Express treats it as an error and skips all the regular middlewares, executing the `error-handling middleware` instead.

- In Express, an `error-handling middleware` function is defined similarly to other middleware, but with `four parameters`.

- When we pass four parameters in the `handler function`, Express treats it as an `error-handling middleware`.

- This Error Handling Middleware also known as `Global Error Handling Middleware`.

- MongoDB is a `document-oriented` NoSQL database system.

- Collection is a group of documents.

- The best thing about MongoDB is that it follows a `nested-document` structure, meaning you don't need to establish `primary and foreign key relationships` like in SQL Server or MySQL. Instead, related data can be stored within a single document, reducing the need for complex joins.

- BSON (Binary JSON) is a `binary representation` of JSON like documents.

- JSON data converts into `BSON` and then store into MongoDB.

- It is designed to be efficient in terms of both storage space and speed of scan and traversal.

- It was developed in 2009 by MongoDB.

- It is a `binary-encoded serialization` of JSON documents.

- BSON compresses and optimizes data storage and retrieval.

- BSON is faster than JSON.
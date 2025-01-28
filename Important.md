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
# IMPORTANT POINTS
• When we write code in JavaScript, it converts into a machine language, then our machine understands the code of JavaScript.

• The `JavaScript Engine` is used to convert JS code into a machine language.

• The JavaScript Engine is basically a medium between Javascript code and a machine, which transforms the code into a machine readable language.

• The first JavaScript Engine called `SpiderMonkey` was created by Netscape.

• Netscape introduced this SpiderMonkey engine in their web browser called `Netscape Navigator`.

• Google introduced the fastest JavaScript engine called the `V8 engine` for their Chrome browser.

• JavaScript v8 engine is written in C++.

• Node.js is an open-source and cross-platform `JavaScript Runtime Environment`.

• Node.js was developed by `Ryan Dahl` in 2009.

• Node JS is associated with `Walmart`, `PayPal`, `Netflix`, `Uber`, `LinkedIn`, `ebay` etc.

• When your application starts getting larger, maintaining a single file becomes a difficult task.

• It is easy to get lost in the codebase and lose track of what a particular piece of code is doing.

• To make it easier to maintain, reuse and organize code, you need to split the code into multiple files.

• This process is called `modularization`.

• There are three types of modules, including; `Local Modules`, `Core Modules`, `Third-Party Modules (External Modules)`.

• Local module is a file with `js` extension, having some chunk of javascript code.

• Core modules are come with the Node.js by default.

• Third-party modules are downloaded with a package manager such as `npm`.

• Initialize Node.js project with the command: `npm init`.

• `package.json` file is primarily used for managing and documenting `metadata` about the project, including its `name`, `version`, `author`, `dependencies`, `scripts`, and other configuration details within json format.

• `package-lock.json` file is generated and updated automatically by npm when installing or updating packages. It is used to lock the exact versions of dependencies installed in the project, ensuring reproducibility and consistent installations across different environments.

• Node.js version `18.11` brings a new feature called `--watch` that allows hot reloading without relying on nodemon.

• `Synchronous` is a sequential & blocking architecture, so the execution of each operation depends on completing the one before it.

• `Sync` is a single-thread, so only one operation or program will run at a time.

• `Sync` is a blocking model and it will only send the server one request at a time and wait for that request to be answered by the server.

• `Asynchronous` is a non-blocking architecture, so the execution of one task isn't dependent on another.

• `Async` is a multi-thread, which means operations or programs can run in parallel.

• `Async` is non-blocking, which means it will send multiple requests to a server.

• Node JS follows `Asynchronous`, but Node JS also have `synchronous methods` to perform synchronous specific tasks.

• Fs module is used to provide `file handling`.

• File handling refers to the process by which data is stored and retrieved from `files` using a program.

• Whether you need to read configuration files, write `log files`, the fs module provides the necessary tools to interact with the file system efficiently.
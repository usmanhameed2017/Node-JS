# MIDDLEWARE IN EXPRESS JS

- `Middleware` functions are functions that have access to the request object `(request)`, the response object `(response)`, and the `next()` middleware function in the application's request-response cycle.

- The `next` middleware function is commonly denoted by a variable named `next`.

- `Middleware` is a request handler that allows you to intercept and manipulate requests and responses before they reach `route handlers`.

- They are the functions that are invoked by the Express.js routing layer.

- `Middleware` is just like a `handler function` that take three parameters:
1. `Request object` to perform manipulation with client request.
2. `Response object` to perform manipulation with response and to add custom properties inside response object.
3. `Next() method` to forward the client request to another middleware or route.

- `Middleware` executes right before routing, meaning it receives the client request before it reaches the intended route.

- Practical implementation of Middleware forwarding `client request` to the intended route.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Middleware 01
app.use((request, response, next) => {
    console.log("This is 1st Middleware");
    next();
});

// Middleware 02
app.use((request, response, next) => {
    console.log("This is 2nd Middleware");
    next();
});

// Middleware 03
app.use((request, response, next) => {
    console.log("This is 3rd Middleware");
    next();
});

// Define Route
app.get("/", (request, response) => response.send("Home Page"));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- Practical implementation of `Middleware:02` ending `client request`.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Middleware 01
app.use((request, response, next) => {
    console.log("This is 1st Middleware");
    next();
});

// Middleware 02
app.use((request, response, next) => {
    console.log("This is 2nd Middleware");
    return response.end("Invalid or suspecious request");
});

// Define Route
app.get("/", (request, response) => response.send("Home Page"));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- Middleware can also send response to the client.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Middleware
app.use((request, response, next) => {
    console.log("This is Middleware");
    return response.json({ data: 'Response From Middleware' });
});

// Define Route
app.get("/", (request, response) => response.send("Home Page"));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- Middleware can also manipulate `request` object by setting a custom property inside request object.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Middleware:01
app.use((request, response, next) => {
    console.log("This is 1st Middleware");
    request.msg = "Message added my Middleware :01";
    next();
});

// Middleware:02
app.use((request, response, next) => {
    console.log("This is 2nd Middleware", request.msg);
    next();
});

// Define Route
app.get("/", (request, response) => response.send("Home Page " + request.msg));


// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- Middleware functions can perform the following tasks: 
1. Execute any code.
2. Make changes to the request and the response objects.
3. End the request-response cycle.
4. Call the next middleware function in the stack.

- `Middleware` is a flexible tool that helps add functionalities like `logging`, `authentication`, `error handling`, and more to Express applications.

#### main.js
```javascript
// Import Modules
const express = require("express");
const fs = require("fs");
const moment = require("moment");

// Express instance
const app = express();

// Middleware
app.use((request, response, next) => {
    // Message
    console.log("This is Middleware");

    // Logging captured content
    const log = `Timestamp: ${moment().format("MMMM-DD-YYYY, hh:mm:ss a")}\nRequest URL: ${request.url}\nRequest Type: ${request.method}\n`;

    // Append logging content to `log.txt` file
    fs.appendFileSync("./log.txt", log);

    // Let the request move onto the intended route
    next();
});

// Define Route
app.get("/", (request, response) => response.send("Home Page"));


// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

> Note: If the current middleware function does not end the request-response cycle, it must call `next()` to pass control to the next middleware function. Otherwise, the request will be left hanging.
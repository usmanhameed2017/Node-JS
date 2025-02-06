# ERROR HANDLING MIDDLEWARE IN EXPRESS JS

### Types Of Errors:
1. Operational Errors `(Predictable)`
2. Programming Errors

#### Operational Errors

- Operational errors are the problems that we can predict that will happen at some point in future. We need to handle them in advance.

##### Examples:
1. User trying to access an `invalid route`.
2. Inputting `invalid data`.
3. Application failed to connect to server.
4. Request timeout.

#### Programming Errors

- Programming errors are simply bugs that we programmers, by mistake, introduces them in our code.

##### Examples:

1. Trying to read property of an `undefined variable`.
2. Using `await` without `async`.
3. Passing a `number` where an `object` is expected.

### Error Handling Middleware

- `Error Handling Middleware` is used to handle `Operational Errors` and not `Programming Errors`.

- In Express.js, `error-handling middleware` is a function designed to catch and manage errors occurring in your `routes` or other `middleware`.

- It allows you to `centralize` error management and handle errors more gracefully in your application.

- When we pass an argument to the `next()` method, Express treats it as an error and skips all the regular middlewares, executing the `error-handling middleware` instead.

- In Express, an `error-handling middleware` function is defined similarly to other middleware, but with `four parameters`.

- When we pass four parameters in the `handler function`, Express treats it as an `error-handling middleware`.

- And the sequence of parameters will be as follows:
1. Error object (`error`)
2. Request object (`request`)
3. Response object (`response`)
4. Next function (`next`)

> Note: The `error` object must be placed 1st in parameter list.

##### Syntax:
```javascript
const errorHandler = (error, request, response, next) => {
    // Handle the error
}
```

- This Error Handling Middleware also known as `Global Error Handling Middleware`.

> Note: Your `error-handling middleware` must be placed `at the end of all routes` to ensure it catches errors from any route or middleware above it.

### Practical Implementation

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Routes
app.get("/", (request, response) => response.send("Home Page"));
app.get("/about", (request, response) => response.send("About Page"));
app.get("/contact", (request, response) => response.send("Contact Page"));

// Invalid url
app.get("*", (request, response, next) => {
    // Instance of error object
    const error = new Error("Something went wrong!");

    // Set custom properties
    error.statusCode = 404;
    error.status = "Failed";

    // Pass to `Error-Handling Middleware`
    next(error);
});

// Error-handling middleware
app.use((error, request, response, next) => {
    // Track middleware execution
    console.log("Error Handling Middleware Called");

    // Safely initialize variables
    const statusCode = error.statusCode || 500;
    const status = error.status || "Error";
    const message = error.message;

    // Send status response
    response.status(statusCode).json({ statusCode, status, message});
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- `500` status code represents `Internal Server Error`.
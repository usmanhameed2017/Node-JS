# ROUTING IN EXPRESS JS

- Routing is made from the word route.

- Routing refers to determining how an application responds to a `client request` to a particular endpoint, which is a URL and a specific HTTP request method (GET, POST, and so on).

- Each route can have one or more handler functions, which are executed when the route is matched. It can handle different types of `HTTP request method`.

```javascript
// Import express
const express = require("express");

// Initilaize express app
const app = express();

// Define Routes //

// Home
app.get("/", (request, response) => {
    response.send("This is Home Page");
});

// About
app.get("/about", (request, response) => {
    response.send("This is About Page");
});

// Contact
app.get("/contact", (request, response) => {
    response.send("This is Contact Page");
});

// Search
app.get("/search", (request, response) => {
    console.log("User searches for", request.query); // Get query parameter object
    response.send("This is Search Page " + request.query.name); // Access and display name by using custom name property
});

// Invalid URL/Request
app.get("*", (request, response) => {
    response.send("404 - Page Not Found");
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("http://localhost:8000"));
```
# ROUTER-LEVEL MIDDLEWARE

- Types of Middleware:

1. Application-level middleware
2. Router-level middleware
3. Error-handling middleware
4. Built-in middleware
5. Third-party middleware

#### Application-Level Middleware

- Bind `application-level middleware` to an instance of the `app` object by using the `app.use()` function.

- `app` represents our whole `Express` application.

#### Router-Level Middleware'

- `Router-level middleware` works in the same way as `application-level middleware`, but it is bound to an instance of `express.Router()`

- By using `Router-Level Middleware`, we can intercept and validate client-request on a specific routes.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Router instance
const router = express.Router();

// Store the reference of router-level middleware in `request_filter` variable.
const request_filter = router.use((request, response, next) => {
    const age = request.query.age;
    if(!age)
    {
        return response.end("Please enter your age");
    }

    if(age < 18)
    {
        return response.end("You are not allowed to visit this website");
    }
    else
    {
        next();
    }
});

// Define Routes (Validate only `/about` route)
app.get("/", (request, response) => response.send("Home Page"));
app.get("/about", request_filter, (request, response) => response.send("About Page"));
app.get("/contact", (request, response) => response.send("Contact Page"));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

> Note: You can also attach and associate your `router` object with `app` object.

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Router instance
const router = express.Router();

// Store the reference of router-level middleware in `request_filter` variable.
router.use((request, response, next) => {
    const age = request.query.age;
    if(!age)
    {
        return response.end("Please enter your age");
    }

    if(age < 18)
    {
        return response.end("You are not allowed to visit this website");
    }
    else
    {
        next();
    }
});

// Define Routes (Validate only `/about` route)
app.get("/", (request, response) => response.send("Home Page"));
router.get("/about", (request, response) => response.send("About Page"));
app.get("/contact", (request, response) => response.send("Contact Page"));

// Associate router object with app object
app.use('/', router);

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```
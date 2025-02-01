# GETTING STARTED WITH EXPRESS JS

- Create a file named as `main.js`.

- Import `express` and store the reference of express into a variable. 

#### main.js
```javascript
// Import express
const express = require("express");

// Initialize express & store the reference in `app` variable
const app = express();

// Define Route
app.get("/", (request, response) => {
    response.send("Response From A Server");
    console.log(`Request ${request.url}`);
    console.log(`Method ${request.method}`);
});

// Create Server
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- This `app.get()` function tells what to do when a `GET` request at the given route is called.

- This `response.send()` function takes an object as input and it sends this to the `requesting client`. Here we are sending the string "Response From A Server"

- This `app.listen()` function binds and listens for connections on the specified `host` and `port`. Port is the only required parameter here.
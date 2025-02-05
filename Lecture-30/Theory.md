# BOOTSTRAP IN EXPRESS JS

- `Bootstrap` is the most popular CSS Framework for developing responsive and mobile-first websites.

- Bootstrap 5 is the newest version of Bootstrap.

- Install Bootstrap using the command: `npm i bootstrap`.

- Now, register the bootstrap path in your express app using `__dirname` and `path` module.

#### main.js
```javascript
// Import Modules
const express = require("express");
const path = require("path");

// Create express instance
const app = express();

// Set view engine to `EJS`
app.set("view engine", "ejs");

// Get bootstrap's css & js file directory
const css = path.join(__dirname, "node_modules", "bootstrap", "dist", "css");
const js = path.join(__dirname, "node_modules", "bootstrap", "dist", "js");

// Register bootstrap's css and js path
app.use('/css', express.static(css));
app.use('/js', express.static(js));

// Define route and render dynamic HTML page
app.get("/", (request, response) => response.render("home", { name: "Usman Hameed" }));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

#### home.ejs
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="css/bootstrap.min.css">
    <title>Home</title>
</head>
<body>

    <!-- Navbar -->
     <%- include('common/nav') %>

    <!-- Greetings -->
    <h4 class="para"> Hello <%= name %> </h4>

    <!-- Footer -->
    <%- include('common/footer') %>

    <script src="js/bootstrap.min.js"></script>
</body>
</html>
```
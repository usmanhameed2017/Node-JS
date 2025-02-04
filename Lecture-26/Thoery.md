# RENDER HTML & JSON IN EXPRESS JS

- To render & serve `HTML` files

#### public/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- Bootstrap 5 CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" 
    rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" 
    crossorigin="anonymous">    
    <link rel="stylesheet" href="style.css">
    <title>Home Page</title>
</head>
<body>

    <!-- Bootstrap Grid System -->
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-12">
                <h2 class="bg-dark text-white text-center py-5 fw-bold"> THIS IS HOME PAGE </h2>
            </div>
        </div>
        <div class="row mt-2">
            <div class="col-md-6 mx-auto">
                <p class="para"> Lorem, ipsum dolor sit amet consectetur adipisicing elit. </p>
            </div>
        </div>
    </div>

</body>
</html>
```

#### public/about.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- Bootstrap 5 CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" 
    rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" 
    crossorigin="anonymous">    
    <link rel="stylesheet" href="style.css">
    <title>About Page</title>
</head>
<body>

    <!-- Bootstrap Grid System -->
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-12">
                <h2 class="bg-dark text-white text-center py-5 fw-bold"> THIS IS ABOUT PAGE </h2>
            </div>
        </div>
        <div class="row mt-2">
            <div class="col-md-6 mx-auto">
                <p class="para"> Lorem, ipsum dolor sit amet consectetur adipisicing elit. </p>
            </div>
        </div>        
    </div>

</body>
</html>
```

#### main.js
```javascript
// Import Modules
const path = require("path");
const express = require("express");

// Store the reference of express in `app` variable
const app = express();

// Store the reference of public folder
const public = path.join(__dirname, "public");

// Serve static files
app.use(express.static(public));

//  Define Routes
app.get('/', (request, response) => response.sendFile(`${public}/index.html`));
app.get('/about', (request, response) => response.sendFile(`${public}/about.html`));
app.get('*', (request, response) => response.sendFile(`${public}/404.html`));

// Listen on 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

### app.use()

- `app.use()` is used to define middleware that executes on every request, regardless of the HTTP method (GET, POST, PUT, DELETE, etc.)

### express.static()

- `express.static()` middleware serves static files like HTML, CSS, JavaScript, and images from a specified directory on the server.

- It automates the process of `serving static files` by mapping URLs directly to files in the specified directory, simplifying configuration and reducing development effort.

- When a client requests a static file, Express.js uses `express.static` to locate and deliver the file directly to the client's browser without additional routing logic.

- To render single `JSON object`

#### main.js
```javascript
const express = require("express");
const app = express();

// Render JSON when `data` url gets hit
app.get('/data', (request, response) => {
    response.send({ 
        name: "Usman Hameed", 
        age: 24, 
        email: "usman@gmail.com" 
    });
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

- To render multiple `JSON objects`

#### main.js
```javascript
const express = require("express");
const app = express();

// Render JSON when `data` url gets hit
app.get('/data', (request, response) => {
    response.send([
        { name: "Usman Hameed", age: 24, email: "usman@gmail.com" },
        { name: "Blial Amir", age: 27, email: "bilal@gmail.com" },
        { name: "Raheel Azam", age: 29, email: "raheel@gmail.com" },
    ]);
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```
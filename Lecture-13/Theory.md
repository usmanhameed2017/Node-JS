# ROUTING IN NODE JS & SERVE HTML PAGES

### Routing
- `Routing` is the mechanism by which requests (as specified by a URL and HTTP method) are routed to the code that handles them.

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
    </div>

</body>
</html>
```

#### public/404.html
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
    <title>404 - Not Found</title>
</head>
<body>

    <!-- Bootstrap Grid System -->
    <div class="container-fluid">
        <div class="row mt-5">
            <div class="col-md-12">
                <h2 class="text-secondary text-center fw-bold"> 404 - PAGE NOT FOUND </h2>
            </div>
        </div>
    </div>

</body>
</html>
```

#### main.js
```javascript
// Import Modules
const http = require("http");
const fs = require("fs");

// Create Server
http.createServer((request, response) => {
    if(request.url === "/")
    {
        // Read index.html file
        fs.readFile("./public/index.html", (error, data) => {
            // Throw Error
            if(error) throw error;

            // Display HTML File
            response.write(data);

            // End Response
            response.end();
        });
    }
    else if(request.url === "/about")
    {
        // Read about.html file
        fs.readFile("./public/about.html", (error, data) => {

            // Throw Error
            if(error) throw error;

            // Display HTML File
            response.write(data);

            // End Response
            response.end();
        });
    }
    else
    {
        // If no url matches
        fs.readFile("./public/404.html", (error, data) => {
            // Throw Error
            if(error) throw error;

            // Display HTML File
            response.write(data);

            // End Response
            response.end();
        });
    }
}).listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```
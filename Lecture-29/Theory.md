# SHARE COMMON CODE IN MULTIPLE PAGES

- Create a folder inside `views` folder and named it as `common`.

- Create files `nav.ejs` and `footer.ejs` inside `common` folder.

- Now, include your `nav` and `footer` in home.ejs file using `include` method and Hyphen `-` signature.

## PRACTICAL IMPLEMENTATION

#### main.js
```javascript
// Import Express
const express = require("express");
const app = express();

// Set view engine to `EJS`
app.set("view engine", "ejs");

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
    <link rel="stylesheet" href="style.css">
    <!-- Bootstrap 5 CDN -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" 
    rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" 
    crossorigin="anonymous">    
    <title>Home</title>
</head>
<body>

    <!-- Navbar -->
    <%- include('common/nav') %>

    <!-- Greetings -->
    <h4> Hello <%= name %> </h4>

    <!-- Footer -->
    <%- include('common/footer') %>

</body>
</html>
```

### Conclusion

- To share and include common code in multiple files, you need to use Hyphen `-` signature with `%` followed by `include()` method.

- Syntax: <%- include('filename') %>
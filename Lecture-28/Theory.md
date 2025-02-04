# LOGICS & LOOPS IN EJS

- To write your Javascript expression inside HTML, use EJS placeholder syntax like this: `<% %>`. 

- And to print any value, use EJS placeholder with `is equal` sign like this: `<%= name %>`.

### Iteration In EJS

#### main.js
```javascript
// Import Express
const epxress = require("express");
const app = epxress();

// Set View Engine To EJS
app.set("view engine", "ejs");

// Define Routes
app.get("/", (request, response) => {

    // Object
    const data = {
        name: "Usman Hameed",
        age: 24,
        email: "usman@gmail.com",
        hobbies:["Coding", "Documentaries", "Swimming"]
    };

    // Send data to `home` page
    response.render("home", { data });
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

#### views/home.ejs
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
</head>
<body>
    <h2> Home Page </h2>
    <h5> Name: <%= data.name %> </h5>
    <h5> Age: <%= data.age %> </h5>
    <h5> Email: <%= data.email %> </h5>
    <h5> Hobbies: </h5>
    <ul>
        <% data.hobbies.forEach((element) => 
        { 
            %>
                <li> <%= element %> </li>
            <%  
        }) 
        %>
    </ul>
</body>
</html>
```

### Logic With `If Else` In EJS

#### main.js
```javascript
// Import express
const express = require("express");
const app = express();

// Set View Engine To `EJS`
app.set("view engine", "ejs");

// Define Routes
app.get("/" , (request, response) => response.render("home", { username: "usman@123", password: "GrayHat" }));

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("Server is started and running at http://localhost:8000"));
```

#### views/home.ejs
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
</head>
<body>
    <h2> Home Page </h2>
    <h4> Login Status </h4>
    <% if(username === "usman@123" && password === "GrayHat")
    {
        %>
            <h5> Login Successful </h5>   
        <%
    }
    else
    {
        %>
            <h5> Login failed! Invalid username or password </h5>
        <%
    }
    %>
</body>
</html>
```
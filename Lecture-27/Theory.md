# EJS TEMPLATE ENGINE IN EXPRESS JS

- EJS (`Embedded JavaScript`) is a popular template engine for Node JS.

- A template engine in web development is a tool that helps you create `dynamic HTML pages`.

- EJS `Embedded JavaScript` is a popular template engine for Node.js that allows you to generate HTML markup with `plain JavaScript`.

- It is specifically useful for creating `dynamic web pages`, as it enables you to embedded JavaScript logic directly within your HTML.

- Template engine helps to create an HTML template with minimal code. Also, it can `inject data` into an HTML template on the client side and produce the final HTML.

### Installation

- Install `EJS` in your project by using `npm`.

##### Command

```javascript
npm i ejs
```

- Now, set template engine as `EJS` by using `set()` method.

- Now, create `views` folder in your project directory and create all `ejs` pages inside it.

- Now, call `render()` function to display pages that created inside `views` folder.

> Note: To write your Javascript expression inside HTML, use EJS placeholder syntax like this: `<% %>` and to print any value, use EJS placeholder with `is equal` sign like: `<%= name %>`.

## PRACTICAL IMPLEMENTATION

#### main.js
```javascript
// Import Express
const express = require("express");

// Store the reference of express in `app` variable
const app = express();

// Set view template engine
app.set('view engine', 'ejs');

// Define route to display dynamic HTML page and send data
app.get("/", (request, response) => {
    response.render('home', { name:"Usman Hameed", age:24, email:"usman@gmail.com" });
});

// Listen on port 8000
app.listen(8000, "localhost", () => console.log("http://localhost:8000"));
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
    <h2> This is Home Page </h2> 
    <h4> Name is <%= name %> </h4>
    <h4> Name is <%= age %> </h4>
    <h4> Name is <%= email %> </h4>
</body>
</html>
```

> Note: To comment `ejs` syntax, replace `=` with `#` and then comment your HTML code.
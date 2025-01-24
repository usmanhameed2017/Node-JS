# BASIC INTRODUCTION

• When a client sends a request to a server, the server listens for it on a specific port number.

• However, ensure that the port number is not already in use by another application.

• Node JS applications often listen on port 3000 or 4000, but those are just conventions, and you don't have to follow them.

• When a client sends a request to a server, it is referred to as an `HTTP Request Header`. 

• When the server processes the request and sends back a response, it is known as an `HTTP Response Header`.

• HTTP Request Header contains some information. Most popular are:

#### Request URL
###### Request Type: (GET, POST, PUT/PATCH, DELETE)

###### User-Agent: (Browser, Operating System information)

• HTTP Response Header contains some information. Most popular are:

#### Content Length

• Contains a total length of a content.

#### Content Type

• Content type can be, `HTML`, `JSON`, `Plain Text`.

#### Status Code

• Contain status code including `(200 OK, 404 Not Found)`.

# HTTP MODULE IN NODE JS (CREATE A SERVER IN NODE JS)

• Node JS has a built-in module called `HTTP`, which allows Node JS to transfer data over the Hyper Text Transfer Protocol (HTTP).

• The HTTP module in Node JS provides utilities for creating HTTP servers and clients.

• It's a fundamental part of Node JS, enabling the handling of `HTTP requests and responses`.

• The HTTP module allows you to create a server using the `http.createServer()` method, which listens for incoming requests and handles them using a `callback` function.

#### main.js
```javascript
// Import HTTP Module
const http = require("http");

// Create A Server
const server = http.createServer((request, response) => {
    response.write("Hello From A Server...!"); // Write Response
    response.end(); // End Response
});

// Listen To Port 8000
server.listen(8000, "localhost", () => {
    console.log("Server started running at http://localhost:8000");
});
```

• Use `setHeader` method to generate your content type.

#### main.js
```javascript
// Import HTTP Module
const http = require("http");

// Create A Server
const server = http.createServer((request, response) => {
    response.setHeader("Content-Type", "text/plain"); // Set Content Type
    response.write("Hello From A Server...!"); // Write Response
    response.end(); // End Response
});

// Listen To Port 8000
server.listen(8000, "localhost", () => {
  console.log("Server started running at http://localhost:8000");
});
```

• Another way of creating a server and listen to a port.

#### main.js
```javascript
// Import HTTP Module
const http = require("http");

// Create A Server
http.createServer((request, response) => {
    response.setHeader("Content-Type", "text/plain"); // Set Content Type
    response.write("Hello From A Server...!"); // Write Response
    response.end(); // End Response
}).listen(8000, "localhost", () => { // Listen To Port 8000
    console.log("Server started running at http://localhost:8000");
});
```
# SOCKET.IO

- `Socket.IO` is a JavaScript library that helps in real-time communication between a server and clients (like web browsers or apps).

- It allows data to be sent and received instantly without refreshing the page.

- Here are some key points about Socket.IO:

### Real-time Communication

- It allows instant data exchange between users, making it perfect for chat apps, live notifications, and multiplayer games.

### Two-way Connection

- Both the server and client can send and receive messages at the same time.

### Fast and Efficient

- It reduces delays, making apps more interactive and responsive.

### Automatic Reconnection

- If the connection is lost, it tries to reconnect automatically.

### Easy to Use

- Provides simple methods to send and receive messages.

### Supports Rooms & Namespaces
 
- Allows grouping of users in different chat rooms or sections.

> Note: Since WebSocket relies on the `HTTP protocol` internally, you need to use the `HTTP module` to create a server. This means you must bind your `Express server` with the `Socket.IO server` to ensure proper communication.

## INSTALLATION

- To install socket.io in your Node.js application; type this command in terminal.

```javascript
npm i socket.io
```

- Attach socket.io CDN on client

```javascript
<script src="/socket.io/socket.io.js"></script>
```

- To establish a connection with server, call `io()` function.

```javascript
const socket = io();
```

## PRACTICAL IMPLEMENTATION

#### index.js
```javascript
const express = require("express");
const http = require("http");
const { Server } = require("socket.io");

// Express app
const app = express();

// Express server
const server = http.createServer(app);

// Middleware plugins
app.use(express.urlencoded({ extended:false }));
app.use(express.json());

// Set template engine
app.set("view engine", "ejs");

// Route
app.get((request, response) => response.render("home"));

// Socket instance
const io = new Server(server); // Bind web socket server with express server

// Socket connection
io.on("connection", (socket) => {
    console.log(`${socket.id} connected!`);

    // Listen on new message
    socket.on("new-message", (message) => {
        console.log(`Message is ${message}`);

        // Send this message back to client
        io.emit("recieved", message);
    });
});

// Start server
server.listen(8000, () => console.log("Server is started and running at http://localhost:8000"));
```

### Home.ejs
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
    <link rel="stylesheet" href="/style.css">
    <link rel="stylesheet" href="/bootstrap.min.css">
</head>
<body>
    <h2 class="bg-dark text-white text-center fw-bold py-5"> CHAT APP </h2>

    <div class="container">
        <div class="row mt-5">
            <div class="col-md-5 mx-auto">
                <form>
                    <div class="form-group">
                        <label> Message </label>
                        <input type="text" id="message" class="form-control" placeholder="Enter Message">
                        <button type="button" id="sendBtn" class="btn btn-outline-primary mt-2"> Send </button>
                    </div>
                </form>
            </div>
        </div>
        <div class="row mt-2">
            <div class="col-md-5 mx-auto">
                <div id="allMessages"> <!-- Message body --> </div>
            </div>
        </div>
    </div>

    <!-- Scripts -->
    <script src="/bootstrap.min.js"></script>
    <script src="/socket.io/socket.io.js"></script>
    <script>
        // Socket connection
        const socket = io();

        // Get inputs
        const button = document.getElementById("sendBtn");
        const allMessages = document.getElementById("allMessages");
        
        // Send message to server
        button.addEventListener("click", function() {
            const message = document.getElementById("message");
            socket.emit("new-message", message.value);
            message.value = "";
        });

        // Listen on recieved to update UI in real-time.
        socket.on("recieved", (message) => {
            const p = document.createElement("p");
            p.innerText = message;
            allMessages.appendChild(p);
        });
    </script>
</body>
</html>
```
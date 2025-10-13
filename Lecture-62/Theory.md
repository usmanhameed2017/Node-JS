# ⚡ SCALE-UP SOCKET.IO USING REDIS ADAPTER

### 📘 Overview

- When running a Node.js app in cluster mode or across multiple instances, each worker or server has its own Socket.IO instance.

- This creates a problem — since **Socket.IO rooms and events are stored in memory**, different workers cannot share socket data.

- To solve this, we use the Redis Adapter. It allows all Socket.IO instances to communicate through Redis **Pub/Sub**, ensuring that events like emit, broadcast, and join room work seamlessly across all workers and distributed servers too.

### 🧠 Core Concept

- The Redis adapter uses two Redis clients:

1. Publisher → Sends events from one worker
2. Subscriber → Receives events in another worker

- This ensures all connected clients across workers and servers remain synchronized.

### ⚙️ Installation
```bash
npm install express cors dotenv socket.io ioredis "@socket.io/redis-adapter" "@socket.io/sticky"
```

---

## 💻 PRACTICAL IMPLEMENTATION

`📂 src/app.js`
```javascript
const express = require("express");
const cors = require("cors");
const cookieParser = require("cookie-parser");
const { corsOptions } = require("./config");
const http = require("http");
const { Server } = require("socket.io");
const Redis = require("ioredis");
const { createAdapter } = require("@socket.io/redis-adapter");

// Initialization
function createApp()
{
    // Initialize Express App
    const app = express();

    // Create HTTP Server
    const server = http.createServer(app);

    // ************* SCALE-UP SOCKET.IO USING REDIS ADAPTER ************* //
    // Redis config
    const pubClient = new Redis({
        host: process.env.REDIS_HOST,
        port: process.env.REDIS_PORT,
        username: process.env.REDIS_USERNAME,
        password: process.env.REDIS_PASSWORD,
        retryStrategy: (times) => Math.min(times * 50, 2000)
    });

    // Sub client
    const subClient = pubClient.duplicate();

    // Initialize Socket.IO
    const io = new Server(server, { cors:corsOptions, adapter:createAdapter(pubClient, subClient) });

    // ************* MIDDLEWARES ************* //
    app.use(cors(corsOptions));
    app.use(cookieParser());
    app.use(express.urlencoded({ extended: true, limit: "100kb" }));
    app.use(express.json({ limit: "100kb" }));

    // Make io available to all routes
    app.use((request, response, next) => {
        request.io = io;
        next();
    });

    // Socket connection
    io.on("connection", (socket) => {
        console.log(`${socket.id}: has connected! Handled by worker: ${process.pid}`);

        // Disconnect
        socket.on("disconnect", () => {
            console.log(`${socket.id}: has disconnected! Handled by worker: ${process.pid}`);
        });
    });

    // ************* ROUTES ************* //
    const authRouter = require("./routes/auth");
    const userRouter = require("./routes/user");

    // Registered routes
    app.use("/api/v1/auth", authRouter);
    app.use("/api/v1/user", userRouter);

    return { server, io };
}

module.exports = createApp;
```

---

`📂 src/index.js`
```javascript
require("dotenv").config();
const connectDB = require("./database/connection");
const cluster = require("cluster");
const os = require("os");
const { setupMaster, setupWorker } = require("@socket.io/sticky");
const createApp = require("./app");

// Port
const port = process.env.PORT || 8000;

// Total CPU Cores
const totalCPUS = os.cpus().length;

// Clusterization
if(cluster.isPrimary) 
{
    console.log(`Primary process ${process.pid} is running`);
    console.log(`Spawning ${totalCPUS} worker processes...`);

    // Attach sticky session master handler
    const { server } = createApp();
    setupMaster(server, { loadBalancingMethod: "ip" }); // Sticky session based on IP

    // Spawn workers
    for(let i = 0; i < totalCPUS; i++) cluster.fork();

    // Restart dead workers
    cluster.on("exit", (worker) => {
        console.log(`Worker ${worker.process.pid} died. Restarting...`);
        cluster.fork();
    });
} 
else 
{
    // Worker setup
    connectDB()
    .then(() => {
        // Attach sticky worker handler
        const { server, io } = createApp();
        setupWorker(io);
        server.on("error", (error) => console.log(`Express app failed to listen! ${error}`));
        server.listen(port, () => console.log(`Server running on port:${port} | PID: ${process.pid}`));
    })
    .catch((error) => console.log(`Database connection failed in worker ${process.pid}: ${error.message}`));
}
```

---

### 🧠 Why We Need Sticky Sessions (@socket.io/sticky)

- Even after using a Redis adapter, **sticky sessions** are essential when using clustering or load balancing.

- When a new WebSocket connection is established, it starts as an **HTTP handshake**.

- If the load balancer sends that HTTP request to Worker A, but future WebSocket packets go to Worker B, then Socket.IO will fail — because Worker B doesn’t have the original connection in memory.

#### 🔒 Sticky Sessions Ensure That:

- Every subsequent request (HTTP + WebSocket upgrade) from a client always goes to the same worker process.

- This guarantees a stable connection and prevents “invalid session” or “socket not found” errors.

#### 💡 In Short:

- “Sticky sessions ensure **consistency**, Redis ensures **synchronization**.”


### 🧰 Best Practices

✅ Always call pubClient.duplicate() for subClient (never reuse the same instance).

✅ Use a retry strategy for stable reconnects.

✅ Combine Redis adapter with sticky sessions in cluster setups.

✅ Use environment variables for Redis credentials.

### 🧱 Architecture Diagram
```javascript
        ┌───────────────┐
        │   LoadBalancer│
        └───────┬───────┘
                │
 ┌──────────────┴──────────────┐
 │          Workers             │
 │  ┌────────┐   ┌────────┐    │
 │  │PID 15332│   │PID 15636│  │
 │  └────┬────┘   └────┬────┘  │
 │       │             │        │
 │  ┌────▼────┐   ┌────▼────┐  │
 │  │ Socket.IO│   │ Socket.IO│ │
 │  └────┬────┘   └────┬────┘  │
 └───────┼─────────────┼────────┘
         │             │
      ┌──▼─────────────▼───┐
      │     Redis Pub/Sub  │
      └────────────────────┘
```
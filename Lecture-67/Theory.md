# 📩 QUEUE SYSTEM IN REDIS

Redis queues are commonly used for:

- Background jobs
- Email processing
- Notification systems
- Task scheduling
- Microservice communication

### ⚙ Installation
```bash
npm i express redis dotenv
```

---

`📂 .env`
```javascript
# PORT
PORT=8000

# REDIS CONFIG
REDIS_HOST="Redis Host"
REDIS_PORT="Redist Port"
REDIS_USERNAME="default"
REDIS_PASSWORD="Redis password"
```

---

`📂 src/redis/connection.js`
```javascript
const Redis = require('ioredis');

// Redis config options
const redisConfigOptions = {
    host: process.env.REDIS_HOST, 
    port: process.env.REDIS_PORT,
    username: process.env.REDIS_USERNAME,
    password: process.env.REDIS_PASSWORD
};

// Connect to redis
const redis = new Redis(redisConfigOptions);

// Error
redis.on('error', (error) => console.error('Failed to connect with Redis', error.message));

// Connect
redis.on('connect', () => console.log('Connected to Redis'));

module.exports = { redisConfigOptions, redis };
```

---

`📂 src/index.js`
```javascript
require("dotenv").config();
const express = require("express");
const { redis } = require("./redis/connection");

// Express app
const app = express();

// Port
const port = process.env.PORT || 8000;

// Middleware
app.use(express.json({ limit: "20kb" }));

// Add to queue
app.route("/addToQueue").post(async (request, response) => {
    const { email } = request.body;

    // Push from right hand-side
    await redis.rpush("emailQueue", JSON.stringify({ email }));

    // Response
    return response.json({ message: "Set on queue", data: email });
});

// Retrieve from queue
app.route("/retrieveFromQueue").get(async (request, response) => {
    // Get from left hand-side
    const raw = await redis.lpop("emailQueue");
    const data = JSON.parse(raw);

    // Response
    return response.json({ message: "Get from Queue", data });
});

// Start express server
app.listen(port, () => console.log(`Server is listening at port:${port}`));
```

---

### 📜 FIFO & LIFO Pattern

1. **FIFO** rpush() -> lpop()

2. **FIFO** lpush() -> rpop()

3. **LIFO** rpush() -> rpop()

3. **LIFO** lpush() -> lpop()

---

## 📌 Redis Queue Commands

| Command    | Purpose                |
| ---------- | ---------------------- |
| `lpush()`  | Insert item from left  |
| `rpush()`  | Insert item from right |
| `lpop()`   | Remove item from left  |
| `rpop()`   | Remove item from right |
| `llen()`   | Get queue size         |
| `lrange()` | Get all queue items    |

---

## 📌 Check Queue Length

```javascript
const total = await redis.llen("emailQueue");

console.log(total);
```

---

## 📌 Retrieve All Queue Data

```javascript
const data = await redis.lrange("emailQueue", 0, -1);

console.log(data);
```

---

## 📌 Real World Use Cases

* Email sending queue
* OTP delivery queue
* Payment processing
* Video processing
* Order processing
* Notification systems
* Background jobs

---

## 📌 Limitation of Raw Redis Queue

Raw Redis queues are simple but have limitations:

* No retry mechanism
* No job delay
* No failed job handling
* No job priority
* No dashboard/monitoring
* No worker management

For production systems, developers often use:

* BullMQ
* Bee Queue
* Agenda

These libraries are built on top of Redis.

---

# ✅ Summary

Redis queues are:

* Fast
* Lightweight
* Easy to implement

They are excellent for learning queue systems and building small-to-medium asynchronous systems.
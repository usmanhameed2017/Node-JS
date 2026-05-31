# PUB/SUB WITH REDIS

- **Pub/Sub** also known as **publisher** and **subscriber**.

- It is a messaging pattern that enables real-time communication between different applications, services, or servers.

- In this model, a **Publisher** sends messages to a specific channel, while **Subscribers** listen to that channel and receive the messages instantly. 

- Publishers do not need to know who the subscribers are, and subscribers do not need to know who published the message. This loose coupling makes the system highly scalable and easy to maintain.

- Redis **Pub/Sub** is commonly used for real-time notifications, chat applications, live updates, event-driven architectures, and communication between multiple server instances.

### Workflow

1. A subscriber subscribes to a channel.
2. A publisher publishes a message to that channel.
3. Redis immediately delivers the message to all **active subscribers** of the channel.

### Real-World Example

Consider a chat application running on multiple servers. When a user sends a message, one server publishes the message to a Redis channel. All other servers subscribed to that channel instantly receive the message and forward it to their connected users, ensuring real-time communication across the system.

### Important Note

Redis Pub/Sub does **not persist messages**. If a subscriber is offline or not connected when a message is published, that message will be lost. Therefore, Pub/Sub is best suited for temporary real-time communication rather than reliable message storage.

### ⚙ Installation
```bash
npm i express ioredis dotenv
```

---

`📂 .env`
```javascript
# PORT
PORT=8000

# REDIS CONFIG
REDIS_HOST="Redis host"
REDIS_PORT="Redis port"
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

`📂 src/redis/subscriber.js`
```javascript
const Redis = require("ioredis");
const { redisConfigOptions } = require("./connection");

// Subscriber instance
const subscriber = new Redis(redisConfigOptions);

// Subscribe
subscriber.subscribe("notifications", (error) => {
    if(error) return console.log(`Failed to subscribe: ${error.message}`);
    return console.log(`Subscribed successfully`);
});

// Listen
subscriber.on("message", (channel, message) => {
    console.log(`Message recieved from channel: ${channel}`);
    console.log("Message:", JSON.parse(message));
});

module.exports = subscriber;
```

---

`📂 src/index.js`
```javascript
require("dotenv").config();
const express = require("express");
const { redis, redisConfigOptions } = require("./redis/connection");
const Redis = require('ioredis');
require("./redis/subscriber");

// Express app
const app = express();

// Port
const port = process.env.PORT || 8000;

// Middleware
app.use(express.json({ limit: "20kb" }));

// Publisher instance
const publisher = new Redis(redisConfigOptions);

app.route("/publish").post(async (request, response) => {
    const { title, content } = request.body;

    // Prepare paylaod
    const payload = {
        title,
        content,
        createdAt: new Date().toISOString()
    };

    // Publish message to notification channel
    const result = await publisher.publish("notifications", JSON.stringify(payload));
    if(!result) return response.status(400).json({ message: `Failed to publish notification: ${result}`, data: null });

    // Response
    return response.status(201).json({ message: "A message has been published to 'notifications' channel", data: payload });
});

// Start express server
app.listen(port, () => console.log(`Server is listening at port:${port}`));
```
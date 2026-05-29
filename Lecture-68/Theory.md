# 🎯 BULLMQ

**BullMQ** is a powerful queue management library built on top of Redis.

It helps developers process background jobs asynchronously such as:

* Sending emails
* Processing payments
* Notifications
* Video processing
* Scheduled tasks

BullMQ provides features like:

* Job retries
* Delayed jobs
* Concurrency
* Rate limiting
* Job monitoring
* Worker management

It is widely used for building scalable and production-grade Node.js applications.

---

### 📌 What Makes It Production Grade?

| Feature            | Purpose                        |
| ------------------ | ------------------------------ |
| `attempts`         | Retry failed jobs              |
| `backoff`          | Delay retries                  |
| `concurrency`      | Process multiple jobs together |
| `removeOnComplete` | Cleanup completed jobs         |
| `removeOnFail`     | Cleanup failed jobs            |


### ⚙ Installation
```bash
npm i express redis bullmq dotenv
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

# GMAIL CREDENTIALS
GMAIL="abc@gmail.com"
GMAIL_APP_PASSWORD="Gmail app password"

# STATIC OTP
OTP=123456
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

`📂 src/queue/emailQueue.js`
```javascript
const { Queue } = require("bullmq");
const { redisConfigOptions } = require("../redis/connection");

// Email Queue
const emailQueue = new Queue("emailQueue", { 
    connection: redisConfigOptions, 
    defaultJobOptions: {
        attempts: 3,
        backoff: { type: 'exponential', delay: 3000 },
        removeOnComplete: 1000,
        removeOnFail: 1000
    }
});

module.exports = emailQueue;
```

---

`📂 src/worker/emailWorker.js`
```javascript
const { Worker } = require("bullmq");
const { redisConfigOptions } = require("../redis/connection");
const sendEmail = require("../service/email");

// Email Worker
const worker = new Worker("emailQueue", async (job) => {
    const { id, name, data } = job;

    // Send OTP email
    if(name === "sendOTPEmail")
    {
        // Destructure from data object
        const { fullName, email, otp_code } = data;

        // Send welcome email
        await sendEmail(email, 'OTP Verification', `<h1> Hello ${fullName}! Here is your OTP ${otp_code} <h1/>`);      
    }

    // Send welcome email
    if(name === "sendWelcomeEmail")
    {
        // Destructure from data object
        const { fullName, email } = data;

        // Send welcome email
        await sendEmail(email, 'Registration', `<h1> Thank you ${fullName} for signing up <h1/>`);    
    }

    // Log
    console.log(`Job ID ${id} has completed!`); 
}, { connection: redisConfigOptions, concurrency: 5 });

// Attach events
worker.on("completed", (job) => console.log(`Job completed!`, job.id, job.name, job.data));
worker.on("failed", (job, error) => console.log(`Job failed!`, job.id, job.name, job.data, error));
```

---

`📂 src/index.js`
```javascript
require("dotenv").config();
const express = require("express");
const { redis } = require("./redis/connection");
const sendEmail = require("./service/email");
const emailQueue = require("./queue/emailQueue");
require("./worker/emailWorker");

// Express app
const app = express();

// Port
const port = process.env.PORT || 8000;

// Middleware
app.use(express.json({ limit: "20kb" }));

// Signup
app.route("/signup").post(async (request, response) => {
    const { fullName, email } = request.body;

    // Add job
    await emailQueue.add("sendOTPEmail", { fullName, email, otp: process.env.OTP });

    // Response
    return response.status(200).json({ message: `We've sent you an OTP to your email ${email}`, data: email });
});

// Verify OTP
app.route("/verify-otp").post(async (request, response) => {
    const { fullName, email, otp } = request.body;
    if(otp !== process.env.OTP) return response.status(400).json({ message: "Invalid OTP", data: null });

    // Add to queue
    await emailQueue.add("sendWelcomeEmail", { fullName, email });

    // Response
    return response.status(200).json({ message: "Thank you for signup", data: email });
});

// Start express server
app.listen(port, () => console.log(`Server is listening at port:${port}`));
```

---

> Note: You can also specify **Job-level** options within third parameter, that will override **Queue-level** default options.

```javascript
// Add job
await emailQueue.add("sendOTPEmail", { fullName, email, otp: process.env.OTP }, { attempts: 2, backoff: { type: "fixed", delay: 10000 } 
});
```
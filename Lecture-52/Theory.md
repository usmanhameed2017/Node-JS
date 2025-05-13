# EXPRESS RATE LIMIT

### What is Rate Limiting?

- **Rate limiting** is a technique to control the number of requests a user can make to a server in a given time window.
- It protects your application from **abuse, brute force attacks, and denial-of-service (DoS) attacks**.
- In APIs, rate limiting is commonly used to prevent a single client from overwhelming the server.

---

### Installing `express-rate-limit`

```bash
npm install express-rate-limit
```

---

### ⚙ Basic Setup
```javascript
const express = require('express');
const rateLimit = require('express-rate-limit');

const app = express();

// Apply to all requests
const limiter = rateLimit({
    windowMs: 1000 * 60 * 15, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: "Too many requests from this IP, please try again later."
});

// Inject on application-level
app.use(limiter);
```

---

### Apply Rate Limit to Specific Routes
```javascript
app.use('/user', limiter, userRouter); // Only applies rate limit to "/user" route
```

---

| **Option**   | **Description**                                                                 |
|--------------|----------------------------------------------------------------------------------|
| `windowMs`   | Time frame for which requests are checked/remembered (in milliseconds)          |
| `max`        | Maximum number of requests allowed from a single IP during `windowMs`           |
| `message`    | Message sent to the client when rate limit is exceeded                          |

---

### Custom Handler Example
```javascript
const limiter = rateLimit({
    windowMs: 1000 * 60 * 1, // 1 minute
    max: 5,
    handler: function (request, response) {
        return response.status(429)
        .json({ success: false, message: "You have exceeded the 5 requests in 1 minute limit!" });
    }
});
```
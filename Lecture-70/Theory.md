# REDIS INSTALLATION ON VPS (PRODUCTION SETUP GUIDE)

This guide explains how to install Redis on a VPS (Ubuntu), secure it so it is **only accessible internally**, and connect it cleanly from a Node.js application.

---

### 1. Where Redis is Installed (Important Concept)

Redis is installed as a system service on Linux:

- Default location: `/etc/redis/redis.conf`
- Binary: `/usr/bin/redis-server`
- Data directory (optional): `/var/lib/redis`

👉 You do NOT manually choose a "destination folder" like in frontend projects.  
Redis runs as a **system-level service**, not a project folder dependency.

---

### 2. Update VPS Packages

```bash
sudo apt update
sudo apt upgrade -y
```

---

### 3. Install Redis Server
```bash
sudo apt install redis-server -y
```

### 4. Start and Enable Redis Service
```bash
sudo systemctl start redis-server
sudo systemctl enable redis-server
```

---

**Check status:**
```bash
sudo systemctl status redis-server
```

---

### 5. Open Redis Configuration File
```bash
sudo nano /etc/redis/redis.conf
```

---

### 6. Secure Redis (VERY IMPORTANT)

#### 6.1 Bind to Localhost Only (Core Security)

- Find this line:
```bash
bind 127.0.0.1 ::1
```

---

- Ensure it is exactly like above.

`Why?`

- This blocks external access

- Only VPS internal processes can access Redis

#### 6.2 Disable Public Access (Protected Mode)

**Find:**

```bash
protected-mode yes
```

---

**Make sure it is:**

```bash 
protected-mode yes
```

---

#### 6.3 Set Redis Password (Recommended)

**Find:**
```bash
# requirepass foobared
```

---

**Uncomment and set:**
```bash
requirepass YourStrongPassword123
```

---

#### 6.4 Ensure Redis Port (Default)
```bash
port 6379
```

- No change needed unless conflict occurs.

### 7. Restart Redis After Changes
```bash
sudo systemctl restart redis-server
```

---

### 8. Verify Redis is NOT Publicly Exposed

**Run:**
```bash
sudo ss -tulpn | grep 6379
```

**Expected output:**
```bash
127.0.0.1:6379
```

**This means Redis is only accessible internally.**

### 9. Extra Security (Firewall Layer)

**If UFW firewall is enabled:**
```bash
sudo ufw status
```

**Deny Redis port:**
```bash
sudo ufw deny 6379
```

### 10. Testing Redis Locally (Server Side)
```bash
redis-cli
```

**If password is set:**
```bash
AUTH YourStrongPassword123
```

**Test:**
```bash
PING
```

**Expected output:**
```bash
PONG
```

### 11. Node.js Redis Connection (Professional Structure)

`📂 src/redis/connection.js`
```javascript
const Redis = require("ioredis");

// Redis config options
const redisConfigOptions = {
    host: "127.0.0.1",   // Local VPS only
    port: 6379, // Default redis port
    password: process.env.REDIS_PASSWORD || "YourStrongPassword123",
    retryStrategy: (times) => return Math.min(times * 50, 2000)
};

// Create Redis connection
const redis = new Redis(redisConfigOptions);

// Connection events
redis.on("error", (error) => console.log("Redis connection error:", error));
redis.on("connect", () => console.log("Redis connected successfully"));

module.exports = { redisConfigOptions, redis };
```

---

> Note: For local redis server setup, exclude **username** property from redis config options.


### 12. Key Takeaway

- Redis in production should never be exposed to public internet

- Best practice is same VPS internal communication (127.0.0.1)

- Cloud Redis is only needed for multi-server scaling
# 🚀 API CACHING WITH REDIS

### 📘 Introduction

- **API caching using ioredis** — a powerful and production-grade Redis library for Node.js.

- **Redis** = *Remote Dictionary Server*

- It’s an **in-memory key-value store** — extremely fast. 

- Ideal for **caching**, **session storage**, **rate limiting**, and more. 

- Data stored in RAM (so it’s very fast) but can persist to disk too.

### ❓ Why use Redis for caching?

| Advantage | Description |
|------------|-------------|
| ⚡ Speed | Data is served from memory — 10–100x faster than DB |
| 💾 Offload | Reduces load on your main database |
| 🔁 TTL support | Auto-expire data after a time |
| 🧹 Simple | Works with key-value pairs, no schema needed |

---

### 🛠 Some Useful Methods
```javascript
// Set cache
await redis.set("key", "value");

// Get cache
const data = await redis.get("key");

// Delete cache
await redis.del("key");

// Flush all keys
await redis.flushall();

// Check if key exists
await redis.exists(key);

// Set expiry
await redit.expire(key, seconds);

// For setting list
redis.pipeline();
pipeline.rpush(key, JSON.stringify(value));
pipeline.expire(key, seconds);
await pipeline.exec();

// For getting list
const items = await redis.lrange(key, 0, -1); // Get all items in a list
return items.map(item => JSON.parse(item));

// For appending new item to list
await redis.rpush(key, JSON.stringify(newItem));
await redis.expire(key, seconds);
```

### 🌐 Create a Free Redis Cloud Account

- Visit **Redis Cloud** (official Redis hosting).

1. Open your browser and go to 👉 [https://redis.com](https://redis.com)
2. Click on **“Get Started for Free”**  
3. Create your free account (sign up with email + password) or with gmail instead.
4. Once logged in, click **“Create Database”**
5. Fill in:
   - **Database name** → e.g., `MyRedisCache`
   - **Region** → choose nearest (e.g., Asia, US, etc.)
   - **Memory limit** → free tier (30–50 MB)
6. Click **Create Database**
7. Wait for a few seconds — it will show **Connection Details**, such as:
   - **Host** → e.g. `redis-12345.c123.us-east-1-3.ec2.cloud.redislabs.com`
   - **Port** → e.g. `12345`
   - **Username** → `default`
   - **Password** → copy this safely
   - **TLS Enabled** → yes (optional)
8. Keep this window open — you’ll need these credentials soon.

🎯 That’s it — your Redis Cloud database is ready!

---

### ⚙️ Installation

```bash
npm i express mongoose dotenv ioredis
```

---

## 💻 PRACTICAL IMPLEMENTATION

`🔐 .env`
```javascript
MONGO_URL="MongoURL"
DB_NAME="YourDatabaseName"

REDIS_HOST="PublicEndPoint"
REDIS_PORT="RedisPort"
REDIS_USERNAME="default"
REDIS_PASSWORD="RedisPassword"
```

`📂 src/Database/connection.js`
```javascript
const { connect } = require("mongoose");

// Connect Mongo DB
const connectDB = async () => {
    try 
    {
        const response = await connect(`${process.env.MONGO_URL}/${process.env.DB_NAME}`);
        console.log(`Database connected to ${response.connection.host}`);
    } 
    catch(error) 
    {
        console.log(error.message);
        process.exit(1);        
    }
};

module.exports = connectDB;
```

`📂 src/models/User.js`
```javascript
const { Schema, model } = require("mongoose");

// Schema
const userSchema = new Schema({
    name:{
        type:String,
        required:true
    },
    age:{
        type:Number,
        required:true
    },
    email:{
        type:String,
        lowercase:true,
        unique:true,
        required:true
    },
    isActive:{
        type:Boolean,
        default:true
    }
});

// Model
const User = model("User", userSchema);

module.exports = User;
```

`📂 src/redis/connection.js`
```javascript
const Redis = require('ioredis');

// Redis configuration
const redis = new Redis({
    host: process.env.REDIS_HOST, 
    port: process.env.REDIS_PORT,
    username: process.env.REDIS_USERNAME,
    password: process.env.REDIS_PASSWORD
});

// Error
redis.on('error', (error) => console.error('Failed to connect with Redis', error.message));

// Connect
redis.on('connect', () => console.log('Connected to Redis'));

module.exports = redis;
```

`📂 src/index.js`
```javascript
require("dotenv").config();
const express = require("express");
const connectDB = require("./Database/connect");
const User = require("./models/user");
const redis = require("./redis/connection");

// Express app
const app = express();

// Middlewares
app.use(express.urlencoded({ extended:false, limit:"50kb" }));
app.use(express.json({ limit:"50kb" }));

// Database connection
connectDB();

/* ================================ ROUTES ================================ */
// Create user
app.post("/user", async (request, response) => {
    const user = await User.create(request.body);
    return response.status(201).json({ message:"User created successfully", data:user, success:true });
});

// Get all users
app.get("/user", async (request, response) => {
    // Get data from Cache
    const cachedData = await redis.get("usersCache");
    if(cachedData)
    {
        // Serving from cache
        console.log("Serving from Cache");
        return response.status(200).json({ message:"Users have been fetched successfully", data:JSON.parse(cachedData), success:true });
    }

    // Fetch fresh data from DB (Database call)
    const users = await User.find({});
    await redis.set("usersCache", JSON.stringify(users), "EX", 60); // Expires in 60 seconds.

    // Serving from database
    console.log("Serving from DB");
    return response.status(200).json({ message:"Users have been fetched successfully", data:users, success:true });
});

// Get single user
app.get("/user/:id", async (request, response) => {
    const id = request.params.id;

    // Get data from Cache
    const cachedData = await redis.get(`userCache:${id}`);
    if(cachedData)
    {
        // Serving from cache
        console.log("Serving from Cache");
        return response.status(200).json({ message:"User has been fetched successfully", data:JSON.parse(cachedData), success:true })
    }

    // Fetch fresh data from DB (Database call)
    const user = await User.findById(id);
    await redis.set(`userCache:${id}`, JSON.stringify(user), "EX", 60); // Expires in 60 seconds.

    // Serving from database 
    console.log("Serving from DB");
    return response.status(200).json({ message:"User has been fetched successfully", data:user, success:true });
});

// Start server
app.listen(8000, () => console.log("Server is started and running at port:8000"));
```

> Note: For your ease, you can create your custom helper functions for caching operations that handles parsing internally and uses native redis methods for maximum optimization.

`📂 src/redis/cache.js`
```javascript
const redis = require("./connection");

// Set String Cache
const setStringCache = async (key, value, seconds = 60) => {
    if(!key || value === undefined || value === null) return false;

    try 
    {
        // Remove old data for extra safety check
        if(await redis.exists(key)) await redis.del(key);

        // Set new string
        await redis.set(key, JSON.stringify(value), "EX", seconds);
        return true;
    } 
    catch (error) 
    {
        console.log("Failed to set string cache", error.message);
        return false;
    }
};

// Get String Cache
const getStringCache = async (key) => {
    if(!key) return null;

    try 
    {
        const data = await redis.get(key);
        return JSON.parse(data);
    } 
    catch(error) 
    {
        console.log("Failed to get string in cache", error.message);
        return null;
    }
};

// Set Cache List
const setListCache = async (key, value, seconds = 300) => {
    if(!key || value === undefined || value === null) return false;

    try 
    {
        // Remove old data for extra safety check
        if(await redis.exists(key)) await redis.del(key);

        // Normalize into array
        const items = Array.isArray(value) ? value : [value];
        if(items.length === 0) return [];

        // Pipeline for performance
        const pipeline = redis.pipeline();
        items.forEach(item => pipeline.rpush(key, JSON.stringify(item)));

        // Expire inside pipeline
        pipeline.expire(key, seconds);

        // Execute
        await pipeline.exec();
        return true;
    } 
    catch (error) 
    {
        console.log("Failed to set list in cache", error.message);
        return false;
    }
};

// Get List Cache
const getListCache = async (key) => {
    if(!key) return null;

    try 
    {
        const items = await redis.lrange(key, 0, -1);
        if(!items || items.length === 0) return null;
        return items.map(item => JSON.parse(item));
    } 
    catch(error) 
    {
        console.log("Failed to get list cache", error.message);
        return null;
    }
};

// Delete Cache
const deleteCache = async (key) => {
    if(!key) return false;

    try 
    {
        await redis.del(key);
        return true;
    } 
    catch(error) 
    {
        console.log("Failed to delete cache", error.message);
        return false;
    }
};

// Add new item to Redis list
const addToListCache = async (key, newItem, seconds = 300) => {
    try 
    {
        await redis.rpush(key, JSON.stringify(newItem));
        await redis.expire(key, seconds);
        return true;
    } 
    catch(error) 
    {
        console.log("Failed to push item to Redis list", error.message);
        return false;
    }
};

module.exports = { 
    setStringCache,
    getStringCache,
    setListCache, 
    getListCache, 
    deleteCache,
    addToListCache 
};
```

---

- Now, you can use these helpers in your code to reduce your extra lines of code and focus more on logic without needing to parse data everytime.

`📂 src/index.js`
```javascript
require("dotenv").config();
const express = require("express");
const connectDB = require("./Database/connect");
const User = require("./models/user");
const { getListCache, setListCache, addToListCache, getStringCache, setStringCache } = require("./redis/cache");

// Express app
const app = express();

// Middlewares
app.use(express.urlencoded({ extended:false, limit:"50kb" }));
app.use(express.json({ limit:"50kb" }));

// Database connection
connectDB();

/* ================================ ROUTES ================================ */
// Create user
app.post("/user", async (request, response) => {
    // Insert into database
    const user = await User.create(request.body);

    // Update list cache
    await addToListCache("users", user);

    // Response
    return response.status(201).json({ message:"User created successfully", data:user, success:true });
});

// Get all users
app.get("/user", async (request, response) => {
    // Get data from Cache
    const cachedData = await getListCache("users");
    if(cachedData)
    {
        // Serving from cache
        console.log("Serving from Cache");
        return response.status(200).json({ message:"Users have been fetched successfully", data:cachedData, success:true });
    }

    // Fetch fresh data from DB (Database call)
    const users = await User.find({});
    await setListCache("users", users, 60); // Expires in 60 seconds.

    // Serving from database
    console.log("Serving from DB");
    return response.status(200).json({ message:"Users have been fetched successfully", data:users, success:true });
});

// Get single user
app.get("/user/:id", async (request, response) => {
    const id = request.params.id;

    // Get data from Cache
    const cachedData = await getStringCache(`user:${id}`);
    if(cachedData)
    {
        // Serving from cache
        console.log("Serving from Cache");
        return response.status(200).json({ message:"User has been fetched successfully", data:cachedData, success:true })
    }

    // Fetch fresh data from DB (Database call)
    const user = await User.findById(id);
    await setStringCache(`user:${id}`, user, 60); // Expires in 60 seconds.

    // Serving from database 
    console.log("Serving from DB");
    return response.status(200).json({ message:"User has been fetched successfully", data:user, success:true });
});

// Start server
app.listen(8000, () => console.log("Server is started and running at port:8000"));
```
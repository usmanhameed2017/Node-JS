# OPTIMIZE API RESPONSES WITH **node-cache**

### 📘 Introduction

- `node-cache` is a simple **in-memory caching module** for Node.js that allows you to temporarily store **key–value** pairs in your server’s memory.

- It helps improve performance by reducing the need to repeatedly fetch or compute data that doesn’t change frequently.

### 🚀 Why Use Caching?

- Every time your application makes a **database query** or calls an **external API**, it consumes time and resources. 

- Caching allows you to store the response for a certain duration so subsequent requests can be served instantly.

**Example use cases:**
- Storing frequently accessed database results. 
- Caching API responses.  
- Reducing load on third-party services.  
- Temporarily saving computed results.

---

### ⚙️ Installation
```javascript
npm install node-cache
```

### 📍 Basic Usage
```javascript
const NodeCache = require("node-cache");
const cache = new NodeCache({ stdTTL: 100, checkperiod: 120 });

// Set value
cache.set("greetings", "Hello, Everyone!");

// Get value
const value = cache.get("greetings");
console.log(value); // Output: Hello, Everyone!

// Delete key
cache.del("greetings");

// Flush all keys
cache.flushAll();
```

### 🛠 Configuration Options
| Option | Type | Default | Description |
|--------|------|----------|-------------|
| **stdTTL** | `number` | `0` | Default time-to-live (in seconds) for cached keys. `0` means no expiration. |
| **checkperiod** | `number` | `600` | Interval (in seconds) at which expired keys are deleted. |
| **useClones** | `boolean` | `true` | If `true`, objects are cloned before being returned or stored. |
| **deleteOnExpire** | `boolean` | `true` | Automatically removes keys after they expire. |

---

### ⏱ API Caching
```javascript
const express = require("express");
const NodeCache = require("node-cache");
const User = require("./models/User");

// Cache valid for 60 seconds
const cache = new NodeCache({ stdTTL: 60 });

// Express app
const app = express();

// Route
app.get("/users", async (request,, response) => {
    // Check cache
    const cachedData = cache.get("usersCache");
    if(cachedData) return response.status(200).json({ message:"Users fetched", data:cachedData, success:true });

    // Fetch fresh data
    const users = await User.find({});

    // Store in cache
    cache.set("usersCache", users);
    return response.status(200).json({ message:"Users fetched", data:users, success:true });
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

> Note: You can also use shorter version of it.

```javascript
const express = require("express");
const NodeCache = require("node-cache");
const User = require("./models/User");

// Cache valid for 60 seconds
const cache = new NodeCache({ stdTTL: 60 });

// Express app
const app = express();

// Route
app.get("/users", async (request,, response) => {
    // Check cache
    let data = cache.get("usersCache");
    if(!data)
    {
        data = await User.find({});
        cache.set("usersCache", data);
    }

    // Response
    return response.status(200).json({ message:"Users fetched", data:data, success:true });
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

---

### ⚠️ Limitations
- Data is stored **in-memory**, so it is lost when the server restarts.
- Not ideal for distributed systems (multiple servers).
- For large-scale caching, prefer **Redis** or **Memcached**.

### 🧩 Best Practice Tips
✅ Use meaningful cache keys.
✅ Set a realistic TTL to balance freshness vs. performance.
✅ Periodically monitor getStats() for cache efficiency.
✅ Avoid storing large datasets in memory.

🔹 `node-cache` is a powerful yet lightweight solution for improving speed and efficiency in small to medium-sized Node.js applications.
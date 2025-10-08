# 🚀 COMPRESSION MIDDLEWARE

### 📘 Overview

- `compression` is an Express middleware that **automatically compresses HTTP responses** using algorithms like **Gzip** or **Brotli** before sending them to clients.

- This reduces response size and **improves loading performance** significantly, especially for large text-based assets like HTML, CSS, and JavaScript.

---

### ⚙️ Installation

```bash
npm install compression
```

🧠 How It Works

When a client (like a browser) makes a request, it often includes the Accept-Encoding header, e.g.:
```bash
Accept-Encoding: gzip, deflate, br
```

- The compression middleware checks this header and automatically compresses the response using the best-supported algorithm.

- If the browser supports Gzip, it sends compressed content using Gzip.

- If it supports Brotli, it uses Brotli for even better compression.

---

### 💻 Basic Setup
```javascript
const express = require("express");
const compression = require("compression");

// Express app
const app = express();

// Enable compression
app.use(compression());

// Route
app.get("/", (request, response) => {
    response.status(200).json({ message:"This response is compressed automatically!", data:{} success:true });
});

app.listen(8000, () => console.log("Server running on port 8000"));
```

---

✅ That’s it!

- Every response sent through Express will now be compressed automatically.

### 🧩 Optional Configuration

- You can customize the behavior of compression using options:
```javascript
app.use(compression({
    level: 6,
    threshold: 1024,
    filter: shouldCompress
}));
```

---


### ⚙️ Options Table
| Option | Type | Default | Description |
|--------|------|----------|-------------|
| **level** | `number` | `-1` | Compression level (`0–9`). Higher means better compression but uses more CPU. |
| **threshold** | `number` | `1024` | Minimum response size (in bytes) required before compression is applied. |
| **filter** | `function` | `shouldCompress(request, response)` | Custom function to decide whether a response should be compressed. |

### 🧪 Example: Custom Filter
```javascript
const shouldCompress = (request, response) => {
    // Don't compress responses with this custom header
    if(request.headers["x-no-compression"]) return false;

    return compression.filter(request, response);
};

app.use(compression({ filter: shouldCompress }));
```

---

- If a client sends a request with header: `x-no-compression: true`

- then compression will be skipped for that response.

### 📈 Benefits of Using Compression
| Advantage | Description |
|------------|-------------|
| ⚡ **Faster load times** | Reduces file size and speeds up client-side rendering. |
| 📉 **Less bandwidth usage** | Minimizes data transfer between server and client. |
| 🌐 **Better SEO ranking** | Search engines favor fast-loading websites. |
| 💰 **Cost efficiency** | Saves hosting and data transfer costs on large-scale apps. |

### 🏁 Conclusion

- `compression` is one of the easiest yet most effective performance boosters for your Express apps.

- It requires minimal setup, works seamlessly, and provides massive speed improvements — especially for data-heavy APIs or SPAs.
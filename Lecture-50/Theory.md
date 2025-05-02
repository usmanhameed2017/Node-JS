# 🌐 CORS (Cross-Origin Resource Sharing)

### 📌 What is CORS?

CORS (Cross-Origin Resource Sharing) is a browser security feature that allows or restricts web applications running at one origin (domain) from making requests to another origin.

In simple terms:  
**CORS defines which external domains are permitted to access your server’s resources.**

---

### ❓ Why is CORS Important?

By default, browsers block requests made from one origin to another for security reasons (like protecting against **Cross-Site Request Forgery** and **data theft**).  
If your frontend and backend are hosted on **different origins**, you need to enable CORS to allow communication.

---

### 📦 Installation

```bash
npm install cors
```

---

### ✅ Basic Setup in Express.js

```javascript
const express = require("express");
const cors = require("cors");

const app = express();

// Enable CORS for all routes
app.use(cors());

app.get("/", (request, response) => {
  response.send("CORS enabled for all origins.");
});

app.listen(3000, () => console.log("Server running at http://localhost:8000"));
```

---

### 🎯 Enable CORS for Specific Origin Only

```javascript
const corsOptions = {
  origin: "http://localhost:5173", // Allow only this origin
};

app.use(cors(corsOptions));
```

---

### ⚙️ CORS Options Explained

| Option             | Description                                                                                 | Example                          |
|--------------------|---------------------------------------------------------------------------------------------|----------------------------------|
| `origin`           | Specifies which origin is allowed to access the resource.                                   | `"http://example.com"` or `true` |
| `methods`          | Specifies allowed HTTP methods.                                                             | `"GET,POST,PUT,DELETE"`          |
| `allowedHeaders`   | Headers allowed in actual request (e.g., Authorization).                                    | `"Content-Type,Authorization"`   |
| `credentials`      | Allows cookies or credentials to be sent in cross-origin requests.                          | `true`                           |
| `optionsSuccessStatus` | Status code to send for successful OPTIONS requests (for legacy browsers).            | `200`                            |

---

### 📌 Enable CORS with Full Options

```javascript
const corsOptions = {
  origin: "http://localhost:5173", // Frontend origin
  methods: ["GET", "POST", "PUT", "DELETE"], // Allowed methods
  allowedHeaders: ["Content-Type", "Authorization"], // Custom headers
  credentials: true, // Allow cookies to be sent
  optionsSuccessStatus: 200, // Support legacy browsers
};

app.use(cors(corsOptions));
```

---

### 🧪 Handling Preflight Requests

Browsers automatically send a **preflight OPTIONS request** before actual requests (for methods like POST/PUT with custom headers).  
CORS middleware automatically handles this if set up properly.

You can also handle it manually if needed:

```javascript
app.options("*", cors(corsOptions));
```

---

### 🧩 Apply CORS to Specific Routes Only

You can enable CORS middleware only on selected routes.

```javascript
const express = require("express");
const cors = require("cors");
const app = express();

const corsOptions = {
  origin: "http://localhost:5173",
  credentials: true,
};

// CORS enabled only for /admins route
app.get("/admins", cors(corsOptions), (request, response) => {
  response.send("Admins route with CORS enabled.");
});

// CORS enabled only for /users route
app.get("/users", cors(corsOptions), (request, response) => {
  response.send("Users route with CORS enabled.");
});

// /productlist route without CORS
app.get("/productlist", (request, response) => {
  response.send("Product list route WITHOUT CORS.");
});
```

📝 **Note:**  
This method is helpful when you want to restrict CORS on sensitive or public routes.

---

### 🔐 CORS with Credentials Example

```javascript
const corsOptions = {
  origin: "http://localhost:5173",
  credentials: true
};

app.use(cors(corsOptions));
```

Then in the frontend (fetch/axios), make sure to set:

```javascript
fetch("http://localhost:8000/api", {
  method: "GET",
  credentials: "include"
});
```

---

### ✅ Conclusion

- CORS is essential when your frontend and backend are on different origins.
- Use the `cors` package to configure it easily in Express.
- Always restrict origins and headers in production for better security.

```javascript
// Recommended: Restrict CORS in production
const corsOptions = {
  origin: process.env.CLIENT_URL,
  credentials: true,
};
app.use(cors(corsOptions));
```
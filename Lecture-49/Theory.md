# ⚙️ REST API Development with Express.js

## 📘 What is an API?

An **API (Application Programming Interface)** allows two systems to communicate.  
In web development, RESTful APIs allow clients (like frontend apps or Postman) to interact with backend servers through HTTP requests like `GET`, `POST`, `PUT`, and `DELETE`.

---

## 🚀 Why Use Express.js for APIs?

- Minimal and fast Node.js framework
- Middleware support
- Simple routing system
- Scalable structure for real-world APIs

---

## 📦 Installation

```bash
npm init -y
npm install express
```

---

## 🛠️ Basic Setup

```javascript
const express = require("express");
const app = express();

// Middleware to parse JSON bodies
app.use(express.json());

// Basic Home Route
app.get("/", (request, response) => {
    response.send("Welcome to Express API");
});

// Start Server
app.listen(8000, () => {
    console.log("API Server running at http://localhost:8000");
});
```

---

## 📌 CRUD API Example — `Users`

We'll build basic **CRUD** operations on a dummy user list.

```javascript
let users = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" }
];

// GET all users
app.get("/api/users", (request, response) => {
    response.json(users);
});

// GET single user
app.get("/api/users/:id", (request, response) => {
    const user = users.find(u => u.id === parseInt(request.params.id));
    if (!user) return response.status(404).send("User not found");
    response.json(user);
});

// POST new user
app.post("/api/users", (request, response) => {
    const newUser = {
        id: users.length + 1,
        name: request.body.name
    };
    users.push(newUser);
    response.status(201).json(newUser);
});

// PUT update user
app.put("/api/users/:id", (request, response) => {
    const user = users.find(u => u.id === parseInt(request.params.id));
    if (!user) return response.status(404).send("User not found");
    user.name = request.body.name;
    response.json(user);
});

// DELETE user
app.delete("/api/users/:id", (request, response) => {
    users = users.filter(u => u.id !== parseInt(request.params.id));
    response.send("User deleted successfully");
});
```

---

## 📥 Testing Tools

You can test your API using tools like:

- [Postman](https://www.postman.com/)
- [Thunder Client](https://www.thunderclient.com/) (VS Code extension)
- `curl` (CLI)

---

## 🧱 HTTP Methods Summary

| Method | Usage                  |
|--------|------------------------|
| GET    | Fetch data             |
| POST   | Create new data        |
| PUT    | Update existing data   |
| DELETE | Remove data            |

---

## 📡 HTTP Status Codes Summary

| Code | Meaning                  |
|------|--------------------------|
| 100  | Continue                 |
| 102  | Processing               |
| 200  | OK                       |
| 201  | Created                  |
| 202  | Accepted                 |
| 307  | Temporary Redirect       |
| 308  | Permanent Redirect       |
| 400  | Bad Request              |
| 401  | Unauthorized             |
| 402  | Payment Required         |
| 404  | Not Found                |
| 500  | Internal Server Error    |
| 504  | Gateway Timeout          |

---


## 🛡️ Best Practices

- Use **status codes** properly (`200`, `201`, `400`, `404`, etc.)
- Always validate user input.
- Structure your project for scalability (e.g: use `routes/`, `controllers/`, `models/`).
- Use **middleware** for logging, error handling, etc.
- Use **dotenv** for environment variables.
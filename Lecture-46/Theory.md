# 📘 NODE.JS SESSIONS WITH `express-session`

### 📄 What is a Session?

A **Session** is a way to store information (in variables) to be used across multiple pages (requests) in a web application.
Unlike client-side storage (like cookies), session data is stored server-side, and only a session ID is shared with the client.

**🔹 Example:**
When you log into a website, your login state is stored in a session.

---

### 🚀 What is `express-session`?

`express-session` is a middleware for Express.js that manages sessions in your Node.js applications.
It creates a session ID and stores it as a cookie in the user's browser while keeping session data server-side.

---

### ✅ Why Use Sessions?

- 📌 Maintain user login state
- 📌 Store user-specific temporary data (like cart items)
- 📌 Secure sensitive information (server-side storage)
- 📌 Track user behavior between different HTTP requests

---

### ⚙ Setup express-session

#### 1. Install express-session

```bash
npm install express-session
```

---

#### 2. Basic Usage in Express App

```javascript
const express = require('express');
const session = require('express-session');

const app = express();

// Middleware setup
app.use(session({
  secret: 'your-secret-key', // Should be a long, random string
  resave: false,             // Don't re-save session if unmodified
  saveUninitialized: false,   // Don't save uninitialized sessions
  cookie: { 
    httpOnly: true,             // Makes the cookie inaccessible to JavaScript (enhances security)
    secure: false,              // Use `true` only if HTTPS is enabled
    sameSite: "strict",         // Ensures that cookies are sent only in same-site requests, preventing CSRF attacks
    maxAge: 1000 * 60 * 60 * 7  // Set session cookie expiration to 7 hours
  }  
}));

// Route to set a session
app.get('/set-session', (request, response) => {
  request.session.username = 'Usman';
  return response.send('Session has been set!');
});

// Route to get a session
app.get('/get-session', (request, response) => {
  if(!request.session.username) return response.send('No session found!');
  return response.send(`Hello, ${request.session.username}`);
});

// Route to destroy a session
app.get('/destroy-session', (request, response) => {
  request.session.destroy(error => {
    if(error) return response.send('Error destroying session');
    return response.send('Session destroyed');
  });
});

// Start server
app.listen(8000, () => console.log('Server running on http://localhost:8000'));
```

---

## 🔡 Important Options in express-session

| Option | Description |
|:-------|:------------|
| `secret` | Used to sign the session ID cookie (must be kept secure). |
| `resave` | Forces the session to be saved back to the session store, even if it wasn't modified. |
| `saveUninitialized` | Forces a session that is "uninitialized" to be saved to the store. |
| `cookie` | Configuration for session cookie (e.g., expiry, security). |
| `store` | By default, session is stored in memory. You can configure external stores like Redis, MongoDB, etc. |

---

## ⚡ Notes & Best Practices

- 🔒 Always keep your `secret` safe and random.
- 🗅 Avoid using the default in-memory store in production. It is **not designed for scalability**.
- 🚀 Use a session store like:
  - `connect-mongo` for MongoDB
  - `connect-redis` for Redis
- 📱 Set `cookie: { secure: true }` if your app uses **HTTPS** for better security.

---

## 💡 Quick Tips

- Set session expiry time to automatically log out users after inactivity.
- Limit the size of session data to only essential information.
- Always validate session existence before allowing access to protected routes.

---

## 📦 Bonus: Sample Flow

> 1. User hits `/set-session` → Session created and stored.
> 2. User hits `/get-session` → Server reads session and responds with data.
> 3. User hits `/destroy-session` → Session deleted.

---

## 🎯 Conclusion

- Sessions allow you to store user-specific information securely.
- `express-session` makes it super simple to implement session management in Node.js apps.
- Always combine sessions with proper security measures (like secure cookies and store encryption).

---

## 📦 Useful Libraries

- [`express-session`](https://www.npmjs.com/package/express-session)
- [`connect-mongo`](https://www.npmjs.com/package/connect-mongo)

## 📘 SESSION MANAGEMENT WITH MONGODB

### 📄 Using MongoDB to Store Sessions

In some applications, especially those with a large number of users or distributed systems, storing sessions on the server side can be more efficient and scalable. One of the most common approaches for doing this is to use **MongoDB** as a session store. This allows you to store session data in a MongoDB database instead of the default in-memory storage, making sessions persistent even if the server restarts.

### 🔹 Why Use MongoDB for Sessions?

- **Scalability**: If your application is running on multiple servers, storing sessions in a database ensures that session data is shared between all servers.
- **Persistence**: Sessions are stored in the database, so they persist even if the server crashes or restarts.
- **Centralized Storage**: All session data is stored in one place, making it easier to manage and monitor.

---

## 🔧 How to Use MongoDB for Session Storage?

To store sessions in MongoDB, you can use the **connect-mongo** package. It provides session storage using MongoDB and integrates easily with **express-session**.

### 📥 Installation

To get started, you need to install the **connect-mongo** package. Run the following command:

```bash
npm install connect-mongo
```

---

Once you've installed `connect-mongo` package, you can configure it to store sessions in your `MongoDB database`. 
- Here's an example of how you can do that:

```javascript
const express = require('express');
const session = require('express-session');
const mongoStore = require('connect-mongo');
const app = express();

// Use express-session middleware
app.use(session({
  secret: 'your-secret-key', // Should be a long, random string
  resave: false,             // Don't re-save session if unmodified
  saveUninitialized: false,   // Don't save uninitialized sessions
  store: mongoStore.create({
    mongoUrl: 'mongodb://localhost:27017/yourDatabase',  // MongoDB URL
    collectionName: 'user_sessions' // Specify the collection where sessions will be stored (Default name will be sessions if not specified)
  }),
  cookie: { 
    httpOnly: true,             // Makes the cookie inaccessible to JavaScript (enhances security)
    secure: false,              // Use `true` only if HTTPS is enabled
    sameSite: "strict",         // Ensures that cookies are sent only in same-site requests, preventing CSRF attacks
    maxAge: 1000 * 60 * 60 * 7  // Set session cookie expiration to 7 hours
  }
}));

// Route to set a session
app.get('/set-session', (request, response) => {
  request.session.username = 'Usman';
  return response.send('Session has been set!');
});

// Route to get a session
app.get('/get-session', (request, response) => {
  if(!request.session.username) return response.send('No session found!');
  return response.send(`Hello, ${request.session.username}`);
});

// Route to destroy a session
app.get('/destroy-session', (request, response) => {
  request.session.destroy(error => {
    if(error) return response.send('Error destroying session');
    return response.send('Session destroyed');
  });
});

// Start server
app.listen(8000, () => console.log('Server running on http://localhost:8000'));
```
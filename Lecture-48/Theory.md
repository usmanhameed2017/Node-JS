# 🛡️ CSRF Token in Node.js

## ❓ What is CSRF?

**CSRF** (Cross-Site Request Forgery) is a type of attack that tricks a user's browser into submitting unintended requests to a web application in which the user is authenticated.

### 📌 Example
Imagine you're logged into a banking site. A malicious website secretly sends a fund transfer request using your session cookie — without your permission. This is CSRF.

---

## ✅ Why Use CSRF Tokens?

- Prevents **unauthorized form submissions**
- Ensures **requests originate from the same site**
- Protects against **cross-origin attacks**
- Verifies **form authenticity**

---

## 📦 Installation

```bash
npm install csurf cookie-parser
```

---

## 🔧 Implementation (Node.js + Express)

### `server.js`

```javascript
const express = require("express");
const connectDB = require("./database/connection");
const cookieParser = require("cookie-parser");
const csrf = require("csurf");
const app = express();

// Connect to MongoDB
connectDB("mongodb://localhost:27017", "auth");

// Serve Static Files
app.use("/public", express.static(__dirname + "/public"));
app.use("/css", express.static(__dirname + "/node_modules/bootstrap/dist/css"));
app.use("/js", express.static(__dirname + "/node_modules/bootstrap/dist/js"));

// Set EJS as the template engine
app.set("view engine", "ejs");

// Parse URL-encoded form data
app.use(express.urlencoded({ extended: true, limit: "20kb" }));

// Parse cookies
app.use(cookieParser());

// CSRF protection middleware (stores CSRF token in a cookie)
const csrfProtection = csrf({ cookie: true });

// Render form with CSRF token
app.get("/", csrfProtection, (request, response) => {
    response.render("form", { csrfToken: request.csrfToken() });
});

// Handle form submission
app.post("/", csrfProtection, (request, response) => {
    response.send(request.body);
});

// Start server
app.listen(8000, () => {
    console.log("Server running at http://localhost:8000");
});
```

---

### 🧾 `form.ejs`

```html
<div class="container mt-5">
    <div class="row">
        <div class="col-md-4 mx-auto">
            <form action="/" method="post">
                <!-- Hidden CSRF Token Field -->
                <input type="hidden" name="_csrf" value="<%= csrfToken %>">

                <!-- Name Field -->
                <div class="form-group">
                    <label for="">Name</label>
                    <input type="text" name="name" class="form-control" placeholder="Enter Name">
                </div>

                <!-- Age Field -->
                <div class="form-group">
                    <label for="">Age</label>
                    <input type="number" name="age" class="form-control" placeholder="Enter Age">
                </div>

                <!-- Submit Button -->
                <div class="form-group mt-2">
                    <button type="submit" class="btn btn-outline-success">Submit</button>
                </div>
            </form>
        </div>
    </div>
</div>
```

---

## 🔐 Key Notes

- The CSRF token is generated per request and must be sent back with each form submission.
- The token is validated automatically by `csurf` middleware.
- If the token is missing or invalid, the request is rejected.

---

## 📁 Dependencies Used
- **cookie-parser** – Parses cookies
- **csurf** – CSRF token generation and validation
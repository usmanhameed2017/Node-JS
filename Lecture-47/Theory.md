# 🍪 COOKIE IN NODE.JS

Cookies are small pieces of data stored on the client side and sent along with HTTP requests. In Node.js applications, cookies are commonly used for managing user sessions, remembering preferences, and tracking user behavior.

---

## 📌 Use Cases of Cookies

Here are some common scenarios where cookies are used in web applications:

- **🔐 User Authentication**  
  Stores authentication tokens or user IDs to maintain login status.

- **🛒 Shopping Cart**  
  Tracks items added to a shopping cart by the user.

- **📢 Flash Messages**  
  Temporarily stores messages (like success or error alerts) that disappear after the next request.

- **🔁 Remember Me / Auto Login**  
  Helps in auto-login features by storing login details securely.

- **🎨 Theme Preferences**  
  Stores user-selected themes (e.g., dark/light mode).

- **🌐 Language Preferences**  
  Saves user's selected language to personalize content.

- **📝 Form Data Preservation**  
  Temporarily saves partially filled form data to restore if the page reloads.

---

## 📦 Installation

To work with cookies in Express, install the `cookie-parser` middleware:

```bash
npm install cookie-parser
```

---

### ⚙️ Setup & Configuration

Include `cookie-parser` in your Express app:
```javascript
const express = require("express");
const cookieParser = require("cookie-parser");
const app = express();

app.use(cookieParser("your-secret-key")); // Enables signed cookies
```

---

### 📥 Setting a Cookie
```javascript
response.cookie("key", "value", {
  httpOnly: true,             // Only server can access the cookie.
  secure: true,               // Sends cookie only over HTTPS.
  sameSite: "strict",         // Restricts cookie to same-site requests to prevent CSRF.
  maxAge: 1000 * 60 * 60 * 7, // Duration in milli-seconds until the cookie expires. (7 hours).
  path: "/specific-URL",      // Limits cookie to specific URL path. (Default path will be "/" which is recommended for authentication).
  signed: true                // Ensures the cookie is signed using a secret (provides tamper protection).
});
```

---

### 📤 Accessing a Cookie

To access cookies sent by the client:
```javascript
const cookieValue = request.cookies.key;
```

---

If the cookie is signed, access it from `signedCookies` property:
```javascript
const cookieValue = request.signedCookies.key;
```

---

### ❌ Removing a Cookie

To clear/delete a cookie from the browser:
```javascript
response.clearCookie("key");
```

---

### 🧾 Cookie Options Explained

| Option     | Description                                                                 | Example Value                     |
|------------|-----------------------------------------------------------------------------|-----------------------------------|
| httpOnly   | Prevents client-side JavaScript from accessing the cookie (security).       | true                              |
| secure     | Sends cookie only over HTTPS.                                               | true for production               |
| sameSite   | Restricts cookie to same-site requests to mitigate CSRF.                    | "strict" / "lax" / "none"         |
| maxAge     | Duration (in ms) until the cookie expires.                                  | 1000 * 60 * 60 * 7                |
| expires    | Absolute date/time when the cookie expires.                                 | new Date(Date.now() + 3600000)    |
| path       | Limits cookie to specific URL path.                                         | "/admin"                          |
| domain     | Specifies the domain for which the cookie is valid.                         | "example.com"                     |
| signed     | Ensures the cookie is signed using a secret (provides tamper protection).   | true                              |

---

### 📚 Conclusion

- Cookies are essential for maintaining state in web applications. 

- With the help of cookie-parser, working with cookies in Express becomes simple and secure. 

- Whether you are handling authentication, storing preferences, or implementing flash messages — cookies play a key role in enhancing the user experience.
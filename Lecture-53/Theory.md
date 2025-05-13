# HELMET PACKAGE IN EXPRESS.JS

## 📌 What is Helmet?

- `Helmet` is a middleware for Express.js designed to help secure applications by setting various HTTP headers automatically.
- It helps protect your app from common web vulnerabilities such as:
  - Cross-Site Scripting (XSS)
  - Clickjacking
  - MIME sniffing
  - Cache control attacks
  - and many more.

---

## 💡 Why Use Helmet?

- HTTP headers are a fundamental part of how clients and servers communicate.
- Helmet adds or modifies headers to enhance security.
- Helps follow security best practices automatically without writing custom logic for each header.

---

## ✒ Installation

```bash
npm install helmet
```

---

### ⚙ Basic Setup
```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Apply Helmet middleware globally
app.use(helmet());

app.get('/', (request, response) => response.send('Hello, your app is protected with Helmet!'));

app.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

---

### Disabling a Specific Header

- By default, **helmet** enables all the security headers. So, if you want to disable a specific module then use this syntax:

```javascript
app.use(helmet({
    contentSecurityPolicy: false,
}));

```

---

### Conclusion

- Helmet is a powerful middleware that boosts the security of your Express apps effortlessly.

- It should be one of the first middleware to use in `production-ready` Express applications.

- It does not fix all vulnerabilities, but it significantly reduces risk by setting smart HTTP headers.

- Enable helmet only on production level. On localhost, you may face issues while requesting from a server.
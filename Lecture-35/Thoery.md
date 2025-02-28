# JSON WEB TOKEN (JWT)

- JWT (JSON Web Token) is a secure way to transfer information between a client and a server.

- It is commonly used for user authentication and authorization ensuring data security.

- It is a token (a string of characters) that carries user data securely between a client and a server.

- It helps in verifying a user’s identity without needing to store session data on the server.

### Work Flow

- The server signs (`creates`) a token when a user logs in.

- The client (`browser`) stores the token.

- The client sends the token with every request.

- The server verifies the token to allow access.

### JWT Structure 

-  It has three parts:
1. `Header` – Contains the algorithm used (like HS256).
2. `Payload` – Stores user data (like ID, role, email).
3. `Signature` – Ensures the token is valid.

### Stateless Authentication

- Since JWT contains all necessary data, the server does not need to store session info.

### Secret Key Must Be the Same 

- When signing (creating) a JWT, the server uses a secret key.

- When verifying the JWT, the same secret key must be used.

- If the secret key is different, the token will be invalid.

### Generating a Token

- JWT provides a built-in method `sign` that takes two arguments
1. `Payload` (user data to store in the token).
2. `Secret Signature` (a private key used for signing).

### Validating a Token

- JWT provides a built-in method `verify` that takes two arguments:
1. `Token` (the JWT string received from the client)
2. `Secret Signature` (the same key used during signing).

> Note: JWT is used for user login systems, API security, and Single Sign-On (SSO).

## PRACTICAL IMPLEMENTAION

- Install jwt using the command:
```javascript
npm i jsonwebtoken
```

- #### index.js
```javascript
// Import 
const jwt = require("jsonwebtoken");

// Secret signature key
const secret = "TeaLover";

// Generate a token
const token = jwt.sign({
    _id:2017,
    name:"Usman Hameed",
    age:24,
    email:"usman@gmail.com"
}, secret);

// Validate token
const user = jwt.verify(token, secret);

// Print in console
console.log(user);
```

> Note: The `sign` method encrypts user data using a secret key, while the `verify` method decrypts it after validating it with the same key.
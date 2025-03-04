# BCRYPT

- `Bcrypt` is a hashing algorithm used to securely store passwords by converting them into an encrypted format that cannot be easily reversed. 

- It helps protect user passwords from being stolen in case of a data breach.

- It adds a salt (a random value) to make each hash unique.

- It uses multiple rounds of encryption to slow down brute-force attacks.

- Bcrypt hashes cannot be `decrypted` back into the original password. Instead, it compares the hashed password with a newly entered password during login.

### Built-in Methods

- `bcrypt.hash(password, 10)` - Encrypts a password with 10 rounds of salting.

- `bcrypt.compare("User input value", hashedPassword)` - Compares a plain password with a hashed password.

- Both `bcrypt.hash()` and `bcrypt.compare()` methods returns a promise.

- The `bcrypt.compare()` method returns a boolean value after completing the process; `true` if the plain password matches the hashed password, otherwise `false`.

## PRACTICAL IMPLEMENTATION

- Install bcrypt using the command:

```javascript
npm i bcrypt
```

#### index.js
```javascript
const bcrypt = require("bcrypt");
const password = "abc";

// Encrypt password
bcrypt.hash(password, 10)
.then(hash => console.log(hash))
.catch(error => console.log(error));

// Match password
bcrypt.compare("abc", "hashedValue")
.then(result => console.log(result))
.catch(error => console.log(error));
```
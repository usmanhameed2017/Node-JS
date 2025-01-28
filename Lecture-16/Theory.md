# IMPORTING JSON IN NODE JS

### JSON

- JSON stands for `JavaScript Object Notation`.

- JSON is a standard text-based and human readable data interchange format used to exchange data between `web clients` and `web servers`.

- JSON is a lightweight format for storing and transporting data.

- JSON is often used when data is sent from a server to a web page (client).

- It can generate and stores the data from user input as well.

- It is a collection of `key-value` pairs where the key must be a string and the value can be any of following types: Number, String, Boolean, Array, Object and NULL.

### Advantages Of JSON

- It is language independent and is supported by all programming languages. 

- It can be used on all platforms.

### JSON Methods In JavaScript

- `JSON.parse()` takes a JSON string and converts it into a JavaScript object.

- `JSON.stringify()` converts a JavaScript object into a JSON string.

#### data.json

```javascript
{
    "id": 17,
    "name": "Usman Hameed",
    "isActive": true,
    "hobbies": ["Reading", "Swimming", "Travelling"],
    "address": {
        "street": "Main Street",
        "city": "Islamabad",
        "postalCode": 12345
    },
    "phoneNumber": null
}
```

#### main.js
```javascript
// Import JSON Data
const data = require("./data.json");

// Print Complete Object
console.log("Complete Object: ", data);

// Print Values Individually
console.log("ID: ", data.id);
console.log("Name: ", data.name);
console.log("Active: ", data.isActive);
console.log("Hobbies: ", data.hobbies);
console.log("City: ", data.address.city);
console.log("Street: ", data.address.street);
console.log("Postal Code: ", data.address.postalCode);
console.log("Phone Number: ", data.phoneNumber);
```

> Note: `require` method automatically converts JSON string into a Javascript object. So, you don't need to use `JSON.parse()` method for it.
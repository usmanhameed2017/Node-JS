# MONGOOSE

- `Mongoose` is a MongoDB `Object Data Modeling` (ODM) library for Node.js that helps developers interact with MongoDB databases easily.

- It provides a structured way to define and manage data using schemas and built-in validation.

- Mongoose simplifies working with MongoDB by providing a structured way to define data models.

- It makes handling MongoDB data easier with schemas, validation, and query functions.

- Mongoose uses schemas to define the structure of documents in a MongoDB collection.

- It ensures data integrity by allowing constraints (e.g. `required fields`, `data types`).

- It provides easy-to-use methods for `Create`, `Read`, `Update`, and `Delete` (CRUD) operations.

- Mongoose allows custom logic to be executed before or after database operations

- It also supports referencing and embedding documents for managing relationships between data.

## USEFUL UTILITIES IN MONGOOSE

#### connect (Built-in method)

- Mongoose provides a built-in method `connect()` to establish a connection with the database. 

- The `connect()` method takes a connection string as an argument and returns a promise, allowing you to handle the connection asynchronously.

#### Schema (Built-in class)

- Mongoose provides a built-in class named `Schema` to define the structure of a collection in MongoDB. 

- It allows you to specify field names, data types, validations, and default values for documents.

#### model (Built-in method)

- Mongoose provides a built-in method named `model()` to create a model for a collection. 

- The `model()` method takes two arguments:

1. The name of the collection (as a string).
2. The schema structure that defines the collection’s fields and rules.

## PRACTICAL IMPLEMENTATION

- Install `mongoose` in your Node.js application using the command:
```javascript
npm i mongoose
```

- To establish a connection with database using `connect()` method provided by `mongoose`.

#### index.js
```javascript
const { connect } = require("mongoose");

connect("mongodb://127.0.0.1:27017/db_name")
.then(() => console.log("Database connected!"))
.catch(error => console.log("Unable to establish a connection with database!", error));
```

- To create a schema and model.

#### models/user.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    name:{
        type:String,
        required:true
    },
    age:{
        type:Number,
        required:true
    },
    email:{
        type:String,
        required:true,
        unique:true,
        lowercase:true
    },
    password:{
        type:String,
        required:true
    },
    role:{
        type:String,
        enum:["Admin", "Moderator", "User"],
        default:"User"
    }
});

// Model
const User = model("User", schema);

module.exports = User;
```

> Note: Always write the collection name in uppercase when using the `model()` method. Mongoose will automatically convert it to lowercase and make it plural when creating the collection in MongoDB. 
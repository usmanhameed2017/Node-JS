# 🚀 BASIC API DEVELOPMENT WITH MYSQL

- You can create basic **API end-points** with MYSQL using `mysql2` package.

### 📦 Installation

```javascript
npm i mysql2
```

---

### ⚙️ Database Configuration

- Configure and connect your database

`📂 src/database/connection.js`

```javascript
const mysql = require("mysql2");

// Database configuration
const db = mysql.createConnection({
    host:"localhost",
    user:"root",
    password:"",
    database:"demo"
});

// Connect to database
db.connect((error) => {
    if(error) return console.log("Failed to connect with database", error.stack);
    console.log("Database connected!");
});

module.exports = db;
```

---

### 📌 Create API End Points

- Now, you can create API end points using `db` instance.

`📂 src/index.js`

```javascript
const express = require("express");
const db = require("./connection");

// Express app
const app = express();

// Middlewares
app.use(express.urlencoded({ extended:true, limit:"20kb" }));
app.use(express.json({ limit:"20kb" }));

/* ======================== API END POINTS ======================== */ 

// Get all users
app.get("/users", (request, response) => {
    // Query
    const query = "SELECT * FROM users";

    // Execute
    db.query(query, (error, data) => {
        if(error) return response.status(500).json({ message:error.message, success:false });
        return response.status(200).json({ data:data, message:"Users have been fetched successfully", success:true });
    });
});

// Create user
app.post("/users", (request, response) => {
    // Extract properties
    const { name, age, email, gender } = request.body;

    // Query
    const query = "INSERT INTO users (name, age, email, gender) VALUES (?, ?, ?, ?)";

    // Execute
    db.query(query, [name, age, email, gender], (error, data) => {
        if(error) return response.status(500).json({ message:error.message, success:false });
        if(data.affectedRows === 0) return response.status(400).json({ message:"Failed to create a user", success:false });
        return response.status(201).json({ data:data.insertId, message:"User has been added successfully", success:true });
    });
});

// Get single user
app.get("/users/:id", (request, response) => {
    // Extract ID
    const id = request.params.id;
    if(!id) return response.status(404).json({ message:"User ID is missing", success:false });

    // Query
    const query = "SELECT * FROM users WHERE id=?";

    // Execute
    db.query(query, [id], (error, data) => {
        if(error) return response.status(500).json({ message:error.message, success:false });
        if(data.length <= 0) return response.status(404).json({ message:"User not found", success:false });
        return response.status(200).json({ data:data, message:"User has been fetched successfully", success:true });
    });
});

// Update user
app.put("/users/:id", (request, response) => {
    // Extract ID
    const id = request.params.id;
    if(!id) return response.status(404).json({ message:"User ID is missing", success:false });

    // Extract properties
    const { name, age, email, gender } = request.body;

    // Query
    const query = "UPDATE users SET name=?, age=?, email=?, gender=? WHERE id=?";

    // Execute
    db.query(query, [name, age, email, gender, id], (error, data) => {
        if(error) return response.status(500).json({ message:error.message, success:false });
        if(data.affectedRows === 0) return response.status(404).json({ message:"User not found", success:false });
        return response.status(200).json({ data:data.affectedRows, message:"User has been updated successfully", success:true });
    });
});

// Delete user
app.delete("/users/:id", (request, response) => {
    // Extract ID
    const id = request.params.id;
    if(!id) return response.status(404).json({ message:"User ID is missing", success:false });

    // Query
    const query = "DELETE FROM users WHERE id=?";

    // Execute
    db.query(query, [id], (error, data) => {
        if(error) return response.status(500).json({ message:error.message, success:false });
        if(data.affectedRows === 0) return response.status(404).json({ message:"User not found", success:false });
        return response.status(200).json({ data:data.affectedRows, message:"User has been deleted successfully", success:true });
    });
});

// Start server
app.listen(8000, () => console.log("Server is running on port 8000"));
```

---
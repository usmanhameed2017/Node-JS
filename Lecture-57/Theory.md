# 🚀 ADVANCED API DEVELOPMENT WITH MYSQL USING SEQUELIZE

### Sequlize

- **Sequelize** is a popular **Object Relational Mapper (ORM)** for Node.js that simplifies interaction with relational databases.  

- It provides a clean and consistent API for performing **CRUD operations** without writing raw SQL queries.  

- With Sequelize, you can easily define models, establish relationships, and query the database using JavaScript methods.  

- It supports multiple SQL databases such as **MySQL, PostgreSQL, SQLite, and MariaDB**.  

- Sequelize makes it easier to build **advanced API endpoints** and manage database operations in a structured way.

- Similar to **Mongoose** (used with MongoDB), Sequelize provides **intuitive and chainable methods** to query MySQL and other SQL databases.

---

### 📦 Installation

```javascript
npm i mysql2 sequelize
```

---

### ⚙️ Database Configuration

- Configure your database

`📂 src/database/connection.js`

```javascript
const { Sequelize } = require("sequelize");

// Database configuration
const sequelize = new Sequelize("usman_db", "root", "", {
    host: "localhost",
    dialect:"mysql"
});

module.exports = sequelize;
```

---

### 🏘 Setup Models

- Define your table schema

`📂 src/models/user.js`

```javascript
const { DataTypes } = require("sequelize");
const sequelize = require("../connection");

// Define schema
const User = sequelize.define("User", {
    fname:{
        type: DataTypes.STRING,
        allowNull: false,
        defaultValue: "Unknown"
    },
    lname:{
        type: DataTypes.STRING,
        allowNull: true,
    },
    age:{
        type: DataTypes.TINYINT,
        allowNull:false
    },
    email:{
        type: DataTypes.STRING,
        allowNull: false,
        unique: true,
        validate: {
            isLowercase: true,
            isEmail: true
        }
    },
    address:{
        type: DataTypes.TEXT,
        allowNull: true
    }
}, { timestamps:true });

module.exports = User;
```

---

### 📌 Create API End Points

- Now, you can create API end points using `User` instance.

`📂 src/index.js`

```javascript
const express = require("express");
const sequelize = require("./connection");
const User = require("./models/user");

// Express app
const app = express();

// Middlewares
app.use(express.urlencoded({ extended:true, limit:"20kb" }));
app.use(express.json({ limit:"20kb" }));

// Connect to database
sequelize.authenticate()
.then(() => {
    console.log("Database connected!");
    app.listen(8000, () => console.log("Server is running on port 8000"));
})
.catch(error => console.log("Failed to connect with database!", error.message));

// Create tables automatically
sequelize.sync()
.then(() => console.log("Tables created!"))
.catch(error => console.log("Failed to create tables!", error.message));

/* ======================== API END POINTS ======================== */

// Get all users
app.get("/users", async (request, response) => {
    try 
    {
        const users = await User.findAll();
        return response.status(200).json({ message:"Users fetched", data:users, success:true });
    } 
    catch (error) 
    {
        return response.status(500).json({ message: error.message, success:false });
    }
});

// Create user
app.post("/users", async (request, response) => {
    try 
    {
        const user = await User.create(request.body);
        if(!user) return response.status(400).json({ message: "Failed to add a user", success:false });
        return response.status(201).json({ message:"Users added", data:user, success:true });
    } 
    catch (error) 
    {
        return response.status(500).json({ message: error.message, success:false });
    }
});

// Get single user
app.get("/users/:id", async (request, response) => {
    try 
    {
        // Find user
        const user = await User.findByPk(request.params.id);
        if(!user) return response.status(404).json({ message: "User not found", success:false });

        // Response
        return response.status(200).json({ message:"User fetched", data:user, success:true });
    } 
    catch (error) 
    {
        return response.status(500).json({ message: error.message, success:false });
    }
});

// Update user
app.put("/users/:id", async (request, response) => {
    try 
    {
        // Find user
        const user = await User.findByPk(request.params.id);
        if(!user) return response.status(404).json({ message: "User not found", success:false });

        // Update
        const updatedUser = await user.update(request.body);
        if(!updatedUser) return response.status(400).json({ message: "Failed to update a user", success:false });

        // Response
        return response.status(200).json({ message:"User updated", data:updatedUser, success:true });
    } 
    catch (error) 
    {
        return response.status(500).json({ message: error.message, success:false });
    }
});

// Delete user
app.delete("/users/:id", async (request, response) => {
    try 
    {
        // Find user
        const user = await User.findByPk(request.params.id);
        if(!user) return response.status(404).json({ message: "User not found", success:false });

        // Delete
        const deletedUser = await user.destroy();
        if(!deletedUser) return response.status(400).json({ message: "Failed to delete a user", success:false });

        // Response
        return response.status(200).json({ message:"User deleted", data:deletedUser, success:true });        
    } 
    catch (error) 
    {
        return response.status(500).json({ message: error.message, success:false });
    }
});
```
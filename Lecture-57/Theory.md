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
        allowNull: false
    },
    lname:{
        type: DataTypes.STRING,
        allowNull: true,
        defaultValue: "-"
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
        // Create
        const user = await User.create(request.body);
        if(!user) return response.status(400).json({ message: "Failed to create a user", success:false });

        // Response
        return response.status(201).json({ message:"User created", data:user, success:true });
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

---

> Note: For **Primary Key** & **Foreign Key** relationship and table referencing:

```javascript
// User model
const User = sequelize.define("User", {
    id: { 
        type: DataTypes.INTEGER,
        primaryKey: true, 
        autoIncrement: true
    },
    name: {
        type: DataTypes.STRING,
        allowNull: false
    }
});

// Post model
const Post = sequelize.define("Post", {
    id: { 
        type: DataTypes.INTEGER, 
        primaryKey: true,
        autoIncrement: true
    },
    title: {
        type: DataTypes.STRING,
        allowNull: false
    },
    content: {
        type: DataTypes.TEXT,
        allowNull: false
    },
    userId: {
        type: DataTypes.INTEGER,
        allowNull: false,
        references: {
            model: "Users",
            key: "id"
        }
    }
});

// Referencing & Associations
User.hasMany(Post, { foreignKey: "userId" });
Post.belongsTo(User, { foreignKey: "userId" });

// Query:01
const users = await User.findAll({ include: Post });

// Query:02
const user = await User.findByPk(7, { include: { model: Post, attributes: ["title", "content"] } });
```

---

### 📕 Server Side Pagination

- For server-side pagination, you need to pass three parameters to sequelize's special method `findAndCountAll`.
1. page
2. limit
3. offset

- `findAndCountAll` will return `count` and `rows`, so that you can prepare your pagination options similar to `mongoose-paginate-v2.`

```javascript
const User = require("../models/user");
const Post = require("../models/post");
const { Op } = require("sequelize");

app.get("/user", async (request, response) => {
    // Get pagination query params
    const page = parseInt(request.query.page) || 1;
    const limit = parseInt(request.query.limit) || 10;
    const offset = (page - 1) * limit;
    const search = request.query.search.trim() || ""; // For searching (Optional)

    // Search filter (Optional)
    const where = search ? { name: { [Op.like]: `%${search}%` } } : {};

    // Fetch users with pagination
    const { count, rows } = await User.findAndCountAll({ 
        where, include: { model: Post, attributes: ["title"] },
        limit, offset, order: [["createdAt", "DESC"]]
    });
    
    // Calculate pages
    const totalPages = Math.ceil(count / limit);

    // Prepare pagination options
    const pagination = {
        docs: rows,
        totalDocs: count,
        limit,
        totalPages,
        page,
        pagingCounter: offset + 1,
        hasPrevPage: page > 1,
        hasNextPage: page < totalPages,
        prevPage: page > 1 ? page - 1 : null,
        nextPage: page < totalPages ? page + 1 : null,        
    };

    // Response
    return response.status(200).json({ message:"Users have been fetched", data:pagination:, success:true });
});
```

---

### ⛓ Pre-Save Hook & Model Level Methods

- You can use `beforeSave` hook within model to hash password efficiently.

- You can also use static methods within model level to keep your controller clean from extra logic.

```javascript
const { DataTypes } = require("sequelize");
const sequelize = require("../connection");
const bcrypt = require("bcrypt");

// Define schema
const User = sequelize.define("User", {
    name:{
        type: DataTypes.STRING,
        allowNull: false
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
    password:{
        type: DataTypes.STRING,
        allowNull: false
    }
}, 
{ 
    timestamps:true,
    hooks:{
        beforeSave: async function(user)
        {
            if(user.changed("password"))
            {
                try 
                {
                    user.password = await bcrypt.hash(user.password, 10);
                    return true;
                } 
                catch(error) 
                {
                    console.log(error.message);
                    return false;
                }
            }
        }
    }    
});

// Compare password
User.prototype.matchPassword = async function(password) 
{
    if(!password) return false;
    try 
    {
        return await bcrypt.compare(password, this.password);
    } 
    catch(error) 
    {
        console.log(error.message);
        return false;
    }
};

module.exports = User;
```
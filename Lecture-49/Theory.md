# ⚙️ REST API Development With Express.js

## 📘 What is an API?

An **API (Application Programming Interface)** allows two systems to communicate.  
In web development, RESTful APIs allow clients (like frontend apps or Postman) to interact with backend servers through HTTP requests like `GET`, `POST`, `PUT`, and `DELETE`.

---

## 🚀 Why Use Express.js for APIs?

- Minimal and fast Node.js framework
- Middleware support
- Simple routing system
- Scalable structure for real-world APIs

---

## 📦 Installation

```bash
npm init -y
npm install express multer mongoose
```

---

## 📌 CRUD API — `Users`

**index.js**
```javascript
const express = require("express");
const connectDB = require("./database/connection");
const userRouter = require("./routes/user");
const app = express();

// Database connection
connectDB("mongodb://localhost:27017/test-db");

// Middleware to parse JSON bodies
app.use(express.json());

// Register route
app.use("/api/v1/user", userRouter);

// Start server
app.listen(8000, () => console.log("API Server running at http://localhost:8000"));
```

---

**database/connection.js**
```javascript
const mongoose = require("mongoose");

// Connect With Mongo DB
const connectDB = async (url) => {
    try 
    {
        const response = await mongoose.connect(url);
        console.log("Database connected to", response.connection.host);
    } 
    catch(error) 
    {
        console.log(error.message);
    }
}

module.exports = connectDB;
```

---

**models/user.js**
```javascript
const { Schema, model } = require("mongoose");

// Schema
const userSchema = new Schema({
    name:{
        type:String,
        trim:true,
        required:true
    },
    age:{
        type:Number,
        trim:true,
        required:true
    },
    email:{
        type:String,
        trim:true,
        unique:true,
        lowercase:true,
        required:true
    },
    gender:{
        type:String,
        trim:true,
        enum:["Male", "Female", "Other"],
        required:true
    },
    profile_pic:{
        type:String,
        trim:true
    },
});

// Model
const User = model("User", userSchema);

module.exports = User;
```

---

**middleware/multer.js**
```javascript
const multer = require("multer");

// Define storage (Required)
const storage = multer.diskStorage({
    destination:(request, file, cb) => {
        return cb(null, "./uploads");
    },
    filename:(request, file, cb) => {
        return cb(null, `${Date.now()}-${file.originalname}`);
    }
});

// Define file filter (Optional)
const fileFilter = (request, file, cb) => {
    if(!file.mimetype.startsWith("image/")) return cb(new Error("Invalid file format"), false);
    return cb(null, true);
}

// Specify file limit to 5MB (Optional)
const limits = { fileSize: 1024 * 1024 * 5 };

// Initialize multer with options
const upload = multer({ 
    storage:storage,        // Required
    fileFilter:fileFilter,  // Optional
    limits:limits           // Optional
});

module.exports = upload;
```

---

**controllers/user.js**
```javascript
const mongoose = require("mongoose");
const User = require("../models/user");
const fs = require("fs");

// Fetch all users
const fetchAllUsers = async (request, response) => {
    try 
    {
        const users = await User.find({});
        return response.status(200).json({ data:users, message:"All users has been fetched successfully" });
    } 
    catch (error) 
    {
        return response.status(500).json({ data:null, message:error.message });
    }
}

// Create new user
const addUser = async (request, response) => {
    try 
    {
        request.body.profile_pic = request.file?.path || null;
        const user = await User.create(request.body);
        return response.status(201).json({ data:user, message:"A new user has been created successfully" });
    } 
    catch (error) 
    {
        if(request.file?.path && fs.existsSync(request.file?.path)) fs.unlinkSync(request.file.path);
        return response.status(500).json({ data:null, message:error.message });
    }
}

// Fetch single user
const fetchSingleUser = async (request, response) => {
    if(!mongoose.isValidObjectId(request.params.id)) return response.status(400).json({ data:null, message:"Invalid MongoDB ID" });

    try 
    {
        const user = await User.findById(request.params.id);
        if(!user) return response.status(404).json({ data:null, message:"User not found" });
        return response.status(200).json({ data:user, message:"User has been fetched successfully" });
    } 
    catch (error) 
    {
        return response.status(500).json({ data:null, message:error.message });
    }
}

// Update user
const updateUser = async (request, response) => {
    if(!mongoose.isValidObjectId(request.params.id)) return response.status(400).json({ data:null, message:"Invalid MongoDB ID" });

    try 
    {
        const user = await User.findById(request.params.id);
        if(!user)
        {
            if(request.file?.path && fs.existsSync(request.file?.path)) fs.unlinkSync(request.file.path);
            return response.status(404).json({ data:null, message:"User not found" });
        }
    
        request.body.profile_pic = request.file?.path || user.profile_pic;
        const updatedUser = await User.findByIdAndUpdate(request.params.id, request.body, { new:true });
    
        if(request.file?.path && fs.existsSync(user?.profile_pic)) fs.unlinkSync(user.profile_pic);
        return response.status(200).json({ data:updatedUser, message:"User has been updated successfully" });        
    }
    catch(error) 
    {
        if(request.file?.path && fs.existsSync(request.file?.path)) fs.unlinkSync(request.file.path);
        return response.status(500).json({ data:null, message:error.message });
    }
}

// Delete user
const deleteUser = async (request, response) => {
    if(!mongoose.isValidObjectId(request.params.id)) return response.status(400).json({ data:null, message:"Invalid MongoDB ID" });

    try 
    {
        const user = await User.findByIdAndDelete(request.params.id);
        if(!user) return response.status(404).json({ data:null, message:"User not found" });
        
        if(user?.profile_pic && fs.existsSync(user?.profile_pic)) fs.unlinkSync(user.profile_pic);
        return response.status(200).json({ data:user, message:"User has been deleted successfully" });
    }
    catch(error) 
    {
        return response.status(500).json({ data:null, message:error.message });
    }
}

module.exports = { fetchAllUsers, addUser, fetchSingleUser, updateUser, deleteUser };
```

---

**routes/user.js**
```javascript
const { Router } = require("express");
const { fetchAllUsers, addUser, fetchSingleUser, updateUser, deleteUser } = require("../controllers/user");
const upload = require("../middleware/multer");

// Initialize router
const userRouter = Router();

// ******* Routes ******* //
userRouter.route("/")
.get(fetchAllUsers) // Fetch all users
.post(upload.single("profile_pic"), addUser); // Add new user

userRouter.route("/:id")
.get(fetchSingleUser) // Fetch single user
.put(upload.single("profile_pic"), updateUser) // Update user
.delete(deleteUser); // Delete user

module.exports = userRouter;
```

---

## 📥 Testing Tools

You can test your API using tools like:

- [Postman](https://www.postman.com/)
- [Thunder Client](https://www.thunderclient.com/) (VS Code extension)
- `curl` (CLI)

---

## 🧱 HTTP Methods Summary

| Method | Usage |
|--------|-------|
| GET    | Used to request data from a specified resource. |
| POST   | Used to create or submit new resources. |
| PUT    | Used to update or replace entire existing resource. |
| PATCH  | Used for partial updates to an existing part of a resource/object. |
| DELETE | Used to delete a specified resource from the server. |
| HEAD   | Similar to GET, but it only requests the headers of a resource,<br>without the actual body content. |

---

## 📡 HTTP Status Codes Summary

| Code | Meaning                  |
|------|--------------------------|
| 100  | Continue                 |
| 102  | Processing               |
| 200  | OK                       |
| 201  | Created                  |
| 202  | Accepted                 |
| 307  | Temporary Redirect       |
| 308  | Permanent Redirect       |
| 400  | Bad Request              |
| 401  | Unauthorized             |
| 402  | Payment Required         |
| 404  | Not Found                |
| 500  | Internal Server Error    |
| 504  | Gateway Timeout          |

---


## 🛡️ Best Practices

- Use **status codes** properly (`200`, `201`, `400`, `404`, etc.)
- Always validate user input.
- Structure your project for scalability (e.g: use `routes/`, `controllers/`, `models/`).
- Use **middleware** for logging, error handling, etc.
- Use **dotenv** for environment variables.
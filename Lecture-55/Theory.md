# GOOGLE & FACEBOOK LOGIN AUTHENTICATION

### 📚 Overview

- Integrate **Google** and **Facebook** login in your Node.js app.
 
- Use **passport.js** and their respective strategies.
 
- Securely handle authentication and redirect users.
 
- Generate **JWT tokens** on success.

---

### 🛠️ Setup Requirements

- Install required packages
```bash
npm install passport passport-google-oauth20 passport-facebook jsonwebtoken dotenv cookie-parser
```

---

### ⏳ Step 01: Initialize Passport
`📂 app.js`
```javascript
const express = require("express");
const cors = require("cors");
const cookieParser = require("cookie-parser");
const passport = require("passport");
require("./service/social-auth");

// Express app
const app = express();

// Middleware configuration
app.use(cors({ 
    origin:"*", 
    credentials:true, 
    methods:["GET", "POST", "PUT", "PATCH", "DELETE"] 
}));

// Initialize passport
app.use(passport.initialize());

// Middlewares
app.use(express.urlencoded({ extended:true, limit:"20kb" }));
app.use(express.json({ limit:"20kb" }));
app.use(cookieParser());

// Routes
const userRouter = require("./routes/user");

// Register routes
app.use("/api/v1/user", userRouter);

module.exports = app;
```

---

### 🏘 Step 02: Setup Model
`📂 models/user.js`
```javascript
const { Schema, model } = require("mongoose");
const bcrypt = require("bcrypt");

// Schema
const schema = new Schema({
    gid:{
        type:String,
        unique:true
    },
    fid:{
        type:String,
        unique:true
    },
    name:{ 
        type:String, 
        required:true,
        trim:true
    },
    email:{ 
        type:String, 
        unique:true,
        required:true,
        trim:true,
        lowercase:true
    },
    profile_image:{
        type:String,
        trim:true
    },
    password:{
        type:String,
        required:true
    }
}, { timestamps:true });

// Hash password
schema.pre("save", async function(next) {
    if(!this.isModified("password")) return next();
    try 
    {
        this.password = await bcrypt.hash(this.password, 10);
        return next();        
    } 
    catch(error) 
    {
        console.log(error.message);
        return null;
    }
});

// Model
const User = model("User", schema);

module.exports = User;
```

---

### 🔐 Step 03: Configure .env
`🔐 .env`
```javascript
GOOGLE_CLIENT_ID='your_google_client_id'
GOOGLE_CLIENT_SECRET='your_google_client_secret'
GOOGLE_CALLBACK_URL='http://localhost:8000/api/v1/user/auth/google/callback'

FACEBOOK_APP_ID='your_facebook_app_id'
FACEBOOK_APP_SECRET='your_facebook_app_secret'
FACEBOOK_CALLBACK_URL='http://localhost:8000/api/v1/user/auth/facebook/callback'

JWT_SECRET='your_jwt_secret'
```

---

### ⚙️ Step 04: Passport Configuration
`📂 service/social-auth.js`
```javascript
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const FacebookStrategy = require('passport-facebook').Strategy;
const User = require('../models/user');


// Google
passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: process.env.GOOGLE_CALLBACK_URL,
}, 
async (accessToken, refreshToken, profile, done) => {

    try 
    {
        // Extract properties
        const gid = profile?.id;
        const firstName = profile?.name?.givenName || "";
        const lastName  = profile?.name?.familyName || "";
        const name = `${firstName} ${lastName}`.trim();
        const email = profile?.emails?.[0]?.value;
        const profile_image = profile?.photos?.[0]?.value;

        // If user already exist in database
        const existingUser = await User.findOne({ $or:[{ gid:gid }, { email:email }] });
        if(existingUser) return done(null, existingUser);
    
        // Create new user
        const createUser = await User.create({
            gid:gid,
            name:name,
            email:email,
            profile_image:profile_image,
            password:null
        });
        return done(null, createUser);
    } 
    catch(error) 
    {
        return done(error, null);
    }
}));


// Facebook
passport.use(new FacebookStrategy({
    clientID: process.env.FACEBOOK_APP_ID,
    clientSecret: process.env.FACEBOOK_APP_SECRET,
    callbackURL: process.env.FACEBOOK_CALLBACK_URL,
    profileFields: ["id", "displayName", "emails", "photos"]
}, 
async (accessToken, refreshToken, profile, done) => {
    try 
    {
        // Extract properties
        const fid = profile?.id;
        const name = profile?.displayName;
        const email = profile?.emails?.[0]?.value;
        const profile_image = profile?.photos?.[0]?.value;

        // Check if user already exists
        const existingUser = await User.findOne({ $or:[{ fid:fid }, { email:email }] });
        if(existingUser) return done(null, existingUser);

        // Create new user
        const createUser = await User.create({
            fid:fid,
            name:name,
            email:email,
            profilePic:profile_image,
            password:null
        });

        return done(null, createUser);
    } 
    catch(error) 
    {
        return done(error, null);
    }
}));
```

---

### 👛 Step 05: Token Generator
`📂 utils/generateToken.js`
```javascript
const jwt = require('jsonwebtoken');

const generateToken = (user) => {
    if (!user) return null;

    try 
    {
        return jwt.sign({
            _id: user._id,
            name: user.name,
            email: user.email
        }, process.env.JWT_SECRET, { expiresIn: '1h' });        
    } 
    catch(error) 
    {
        console.log(error.message);
        return null;
    }
};

module.exports = generateToken;
```

---

### 🔁 Step 06: Routes
`📂 routes/user.js`
```javascript
const { Router } = require("express");
const { googleLogin, facebookLogin } = require("../controllers/user");
const passport = require("passport");

// Router instance
const userRouter = Router();

// Login as google
userRouter.route('/auth/google').get(passport.authenticate('google', { scope:['profile', 'email'], prompt:"select_account" }));
userRouter.route('/auth/google/callback').get(passport.authenticate('google', { session: false }), googleLogin);

// Login as facebook
userRouter.get('/auth/facebook', passport.authenticate('facebook', { scope: ['email'] }));
userRouter.get('/auth/facebook/callback', passport.authenticate('facebook', { session: false }), facebookLogin);
```

---

### ⚙️ Step 07: Controllers
`📂 controllers/user.js`
```javascript
const User = require("../models/user");
const { generateToken } = require("../utils/authToken");
const { cookieOptions } = require("../constants");

// Login as gmail
const googleLogin = async (request, response) => {
    if(!request.user) return response.status(404).json({ message:"User not found", success:false });

    // Generate access token
    const accessToken = generateToken(request.user);
    if(!accessToken) return response.status(500).json({ message:"Failed to generate access token", success:false });

    // Send response
    return response.status(200)
    .cookie("accessToken", accessToken, cookieOptions)
    .redirect("http://localhost:5173/home");
}

// Login as facebook
const facebookLogin = (request, response) => {
    if(!request.user) return response.status(404).json({ message:"User not found", success:false });

    // Generate access token
    const accessToken = generateToken(request.user);
    if (!accessToken) return response.status(500).json({ message: "Failed to generate access token", success: false });

    // Send response
    return response.status(200)
    .cookie("accessToken", accessToken, cookieOptions)
    .redirect("http://localhost:5173/home");
}

module.exports = { googleLogin, facebookLogin };
```

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
    origin: "*", 
    credentials: true, 
    methods:["GET", "POST", "PUT", "PATCH", "DELETE"] 
}));

// Initialize passport
app.use(passport.initialize());

// Middlewares
app.use(express.urlencoded({ extended: true, limit: "20kb" }));
app.use(express.json({ limit: "20kb" }));
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

// Password condition
function isPasswordRequired()
{
    return !this.gid && !this.fid;
}

// Schema
const userSchema = new Schema({
    gid: { type: String, trim: true },
    fid: { type: String, trim: true },
    name: { type: String, required: true, trim: true },
    email: { type: String, unique: true, required: true, trim: true, lowercase: true },
    profile_image: { type: String, trim: true },
    password: { type: String, required: isPasswordRequired },
    status: { type: String, trim: true, enum: ["pending", "active", "flagged"], default: "pending" },
}, { timestamps:true });

// Social authentication indexes
userSchema.index({ gid: 1 }, { unique: true, sparse: true });
userSchema.index({ fid: 1 }, { unique: true, sparse: true });

// Hash password
userSchema.pre("save", async function(next) {
    if(!this.isModified("password")) return next();
    try 
    {
        this.password = await bcrypt.hash(this.password, 10);
        return next();        
    } 
    catch(error) 
    {
        console.log(error.message);
        throw new Error(`Failed to hash password: ${error.message}`);
    }
});

// Model
const User = model("User", userSchema);

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
const ApiError = require('../utils/ApiError');

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

        // Validate metadata
        if(!gid || !email) throw new ApiError(400, "Google did not provide the required account information");

        // If user already exist in database
        let user = await User.findOne({ gid });
        if(user) return done(null, user);

        // Check whether email is already registered via normal auth
        user = await User.findOne({ email });
        if(user) throw new ApiError(409, "An account with this email already exists. Please sign in using your existing login method");
    
        // Create new user
        const user = await User.create({ gid, name, email, profile_image, status: "active" });
        return done(null, user);
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

        // Validate metadata
        if(!fid || !email) throw new ApiError(400, "Facebook did not provide the required account information");

        // Check if user already exists
        let user = await User.findOne({ fid });
        if(user) return done(null, user);

        // Check whether email is already registered via normal auth
        user = await User.findOne({ email });
        if(user) throw new ApiError(409, "An account with this email already exists. Please sign in using your existing login method");

        // Create new user
        const user = await User.create({ fid, name, email, profile_image, status: "active" });
        return done(null, user);
    } 
    catch(error) 
    {
        return done(error, null);
    }
}));

// Google initializers and callbacks
const googleAuthInit = passport.authenticate('google', {
    scope: ['profile', 'email'], 
    prompt: "select_account" 
});

const googleAuthCallback = passport.authenticate('google', {
    session: false, 
    failureRedirect: `frontendDomain/login` 
});

// Facebook initializers and callbacks
const facebookAuthInit = passport.authenticate('facebook', {
    scope: ['email'], 
    prompt: "select_account" 
});

const facebookAuthCallback = passport.authenticate('facebook', {
    session: false, 
    failureRedirect: `frontendDomain/login` 
});

module.exports = { googleAuthInit, googleAuthCallback, facebookAuthInit, facebookAuthCallback };
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
const { googleAuthInit, googleAuthCallback, facebookAuthInit, facebookAuthCallback } = require("../service/social-auth");

// Router instance
const userRouter = Router();

// Login as Google
userRouter.route('/auth/google').get(googleAuthInit);
userRouter.route('/auth/google/callback').get(googleAuthCallback, googleLogin);

// Login as Facebook
userRouter.get('/auth/facebook', facebookAuthInit);
userRouter.get('/auth/facebook/callback', facebookAuthCallback, facebookLogin);
```

---

### ⚙️ Step 07: Controllers
`📂 controllers/user.js`
```javascript
const User = require("../models/user");
const { generateToken } = require("../utils/authToken");
const { cookieOptions } = require("../constants");
const ApiError = require("../utils/ApiError");

// Login as Google
const googleLogin = async (request, response) => {
    // Validate
    const user = request.user;
    if(!user) throw new ApiError(401, "Google authentication failed");
    if(user.status !== "active") throw new ApiError(403, "Your account is not eligible for login");

    // Generate access token
    const accessToken = generateToken(user);
    if(!accessToken) throw new ApiError(500, "Failed to generate access token");

    // Send response
    return response.status(200)
    .cookie("accessToken", accessToken, cookieOptions)
    .redirect("http://localhost:5173/home");
}

// Login as Facebook
const facebookLogin = (request, response) => {
    // Validate
    const user = request.user;
    if(!user) throw new ApiError(401, "Facebook authentication failed");
    if(user.status !== "active") throw new ApiError(403, "Your account is not eligible for login");

    // Generate access token
    const accessToken = generateToken(user);
    if(!accessToken) throw new ApiError(500, "Failed to generate access token");

    // Send response
    return response.status(200)
    .cookie("accessToken", accessToken, cookieOptions)
    .redirect("http://localhost:5173/home");
}

module.exports = { googleLogin, facebookLogin };
```

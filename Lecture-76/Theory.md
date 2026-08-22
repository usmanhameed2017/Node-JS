# AUTHENTICATION & AUTHORIZATION IN GRAPHQL 

To implement user authentication in **GraphQL**, we can use `context` property through GraphQL configuration.

### Practical Implementation
`📂 utils/accessToken.js`
```javascript
const jwt = require("jsonwebtoken");

// Secret
const secret = '123';

// Generate access token
const generateAccessToken = (payload) => {
    try
    {
        if(!payload) return null;
        return jwt.sign({ 
            id: payload.id, 
            name: payload.name,
            role: payload.role
        }, secret, { expiresIn: '1d' });
    }
    catch(error)
    {
        console.log("Failed to generate access token", error.message);
        return null;
    }
};

// Verify access token
const verifyAccessToken = (token) => {
    try
    {
        if(!token) return null;
        return jwt.verify(token, secret);
    }
    catch(error)
    {
        console.log("Failed to verify access token", error.message);
        return null;
    }
};

// Get access token
const getAccessToken = (request) => {
    try
    {
        if(!request) return null;
        const accessToken = request.cookies?.accessToken || null;
        return accessToken;
    }
    catch(error)
    {
        console.log("Failed to get access token", error.message);
        return null;
    }
};

module.exports = { generateAccessToken, verifyAccessToken, getAccessToken };
```

`📂 graphql/middlewares/auth.js`
```javascript
const { getAccessToken, verifyAccessToken } = require("../../utils/accessToken");

// Authentication
const authentication = (request, response) => {
    // Get access token
    const accessToken = getAccessToken(request);
    if(!accessToken) return response.status(401).json({ message: "Access token is missing", success: false });

    // Verify access token
    const user = verifyAccessToken(accessToken);
    if(!user) return response.status(401).json({ message: "Invalid access token", success: false });

    request.user = user;
};

// Authorization
const authorization = (request, response, roles = []) => {
    if(!roles.includes(request.user.role)) return response.status(403).json({ message: "Access denied", success: false });
};

module.exports = { authentication, authorization };
```

`📂 app.js`
```javascript
const express = require("express");
const cors = require("cors");
const cookieParser = require("cookie-parser");
const apolloServer = require("./graphql");
const { expressMiddleware } = require("@as-integrations/express5");
const { authentication } = require("./graphql/middleware/auth");

// Initialize express app
async function startApp()
{
    // Express app instance
    const app = express(); 

    // Middleware
    app.use(cors({ origin: "*", credentials: true, allowedHeaders:["Content-Type", "Authorization"] }));
    app.use(express.urlencoded({ extended: true, limit: '20kb' }));
    app.use(express.json({ limit: '20kb' }));
    app.use(cookieParser());

    // Start apollo server
    await apolloServer.start();

    // Register GraphQL middleware
    app.use("/graphql", expressMiddleware(apolloServer, { context: ({ req: request, res: response }) => {
        authentication(request, response);
        return { request, response };
    } }));   

    // REST API Endpoint
    app.get("/api/v1/health", (request, response) => response.status(200).json({ message: "OK" }));

    // Start express app
    app.on("error", (error) => console.log(`Failed to start express app ${error.message}`));
    app.listen(8000, () => console.log(`Server is up and running at PORT ${8000}`));       
}

module.exports = startApp;
```

`📂 server.js`
```javascript
require("dotenv").config();
const startApp = require("./app");
const connectDB = require("./database/connection");

// Connect with DB
(async () => {
    try
    {
        // Connect DB
        await connectDB();

        // Start app
        await startApp();
    }
    catch(error)
    {
        console.log(error);
    }
})();
```

`📂 graphql/user/resolvers.js`
```javascript
const User = require("../../models/userModel");
const { authorization } = require("../middleware/auth");

// Queires
const queries = {
    // Fetch all users
    fetchAllUsers: async (parent, { page = 1, limit = 10 }, context, info) => {
        // Authorize admin
        const { request, response } = context;
        authorization(request, response, ["admin"]);
        
        // Fetch
        const users = await User.aggregatePaginate([
            // Sort
            { $sort: { createdAt: -1 } },

            // Projection
            { $project: { name: 1, age: 1, email: 1 } },
        ], { page, limit });
        return users;
    },

    // Fetch user by ID
    fetchUser: async (_, { id }, context) => {
        // Authorize admin
        const { request, response } = context;
        authorization(request, response, ["admin"]);
        
        // Fetch
        const user = await User.findById(id);
        return user;
    }
};

// Mutations
const mutations = {
    // Create user
    createUser: async (_, { name, age, email }, context) => {
        // Authorize admin
        const { request, response } = context;
        authorization(request, response, ["admin"]);
        
        // Create
        const user = await User.create({ name, age, email });
        return user ? true : false;
    },

    // Update user
    updateUser: async (_, { id, name, age, email }, context) => {
        // Authorize admin
        const { request, response } = context;
        authorization(request, response, ["admin"]);
        
        // Update
        const user = await User.findByIdAndUpdate(id, { $set: { name, age, email } });
        return user ? true : false;
    },

    // Delete user
    deleteUser: async (_, { id }, context) => {
        // Authorize admin
        const { request, response } = context;
        authorization(request, response, ["admin"]);
        
        // Delete
        const user = await User.findByIdAndDelete(id);
        return user ? true : false;
    }    
};

// Resolver contain queries and mutations
const resolvers = { queries, mutations };

module.exports = { resolvers };
```
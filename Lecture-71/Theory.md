# GRAPHQL

GraphQL is a query language for APIs and a runtime for executing those
queries.

Instead of creating multiple REST endpoints, GraphQL exposes **one
endpoint** (usually `/graphql`) where the client requests exactly the
data it needs.

### Benefits

-   Single endpoint
-   Fetch only required fields
-   Strongly typed schema
-   Supports Queries, Mutations and Subscriptions
-   Reduces over-fetching and under-fetching

------------------------------------------------------------------------

### Apollo Server Installation
``` bash
npm install @apollo/server @as-integrations/express5 graphql
```

### For Express integration
``` bash
npm install express dotenv cors mongoose mongoose-aggregate-paginate-v2
```

------------------------------------------------------------------------

## Core Concepts

### 1. Type Definitions (Schema)

The schema describes what data is available.

``` graphql
type User {
  _id: ID!
  name: String!
  age: Int!
  email: String!
}

type Query {
  fetchUsers(page: Int!, limit: Int!): [User]
}

type Mutation {
  createUser(name: String!, age: Int!, email: String!): User
}
```

-   `type` → Object definition
-   `!` → Required field
-   `[User]` → Array of users

------------------------------------------------------------------------

### 2. Resolvers

Resolvers contain the business logic.

## Practical Implementation

`📂 database/connection.js`
``` javascript
const mongoose = require("mongoose");

// Connect DB
const connectDB = async () => {
    try 
    {
        const response = await mongoose.connect(process.env.MONGO_URL);
        console.log(`Database connected to ${response.connection.host}`);
    } 
    catch (error) 
    {
        console.log(error.message);
        process.exit(1);
    }
};

module.exports = connectDB;
```

`📂 models/userModel.js`
``` javascript
const { Schema, model } = require("mongoose");
const aggregatePaginate = require("mongoose-aggregate-paginate-v2");

// Schema
const userSchema = new Schema({
    name: { type: String, trim: true, required: true },
    age: { type: Number },
    email: { type: String, trim: true, required: true }
}, { timestamps: true });

// Add plugin
userSchema.plugin(aggregatePaginate);

// Model
const User = model("User", userSchema);

module.exports = User;
```

`📂 graphql/server.js`
``` javascript
const { ApolloServer } = require("@apollo/server");
const User = require("../models/userModel");

// Initialize apollo server
const apolloServer = new ApolloServer({
    typeDefs: `
        type User {
            _id: ID!
            name: String!
            age: Int!
            email: String!
        }

        type UserPagination {
            docs: [User]
            totalDocs: Int!
            totalPages: Int!
            page: Int!
            limit: Int!
            hasPrevPage: Boolean!
            hasNextPage: Boolean!
            prevPage: Int
            nextPage: Int
        }        

        type Query {
            fetchUsers(page: Int, limit: Int): UserPagination
            fetchUserById(_id: ID!): User
        }
    `,
    
    resolvers: {
        Query: {
            // Fetch all users with pagination
            fetchUsers: async (_, { page = 1, limit = 10 }) => {
                const users = await User.aggregatePaginate([
                    // Sort
                    { $sort: { createdAt: -1 } },

                    // Projection
                    { $project: { _id: 1, name: 1, age: 1, email: 1 } }
                ], { page, limit });
                return users;
            },

            // Fetch single user
            fetchUserById: async (_, { _id }) => {
                const user = await User.findById(_id).lean();
                return user;
            }            
        }
    }
});

module.exports = apolloServer;
```

`📂 app.js`
``` javascript
const express = require("express");
const cors = require("cors");

// Express app
const app = express(); 

// Middleware
app.use(cors({ origin: "*" }));
app.use(express.urlencoded({ extended: true, limit: '20kb' }));
app.use(express.json({ limit: '20kb' }));

// REST API Endpoint
app.get("/api/v1/health", (request, response) => response.status(200).json({ message: "OK" }));

module.exports = app;
```

`📂 server.js`
``` javascript
require("dotenv").config();
const app = require("./app");
const connectDB = require("./database/connection");
const apolloServer = require("./graphql/server");
const { expressMiddleware } = require("@as-integrations/express5");

// Connect with DB
(async () => {
    try
    {
        // Connect DB
        await connectDB();

        // Start apollo server
        await apolloServer.start();

        // Register GraphQL middleware
        app.use("/graphql", expressMiddleware(apolloServer)); 

        // Start express app
        app.on("error", (error) => console.log(`Failed to start express app ${error.message}`));
        app.listen(8000, () => console.log(`Server is up and running at PORT ${8000}`));    
    }
    catch(error)
    {
        console.log(error);
    }
})();
```

# GraphQL Flow

    Client
       │
       ▼
    POST /graphql
       │
       ▼
    Apollo Server
       │
       ├── typeDefs (Schema Validation)
       └── Resolvers (Business Logic)
                 │
                 ▼
              Database

------------------------------------------------------------------------

# Summary

-   **GraphQL** defines what data clients can request.
-   **typeDefs** describe the API structure.
-   **Resolvers** execute the actual logic.
-   **Apollo Server** connects the schema with the resolvers.
-   Common packages: `graphql`, `@apollo/server`, and Express
    integration packages.
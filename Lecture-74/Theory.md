# MODULARIZE CODEBASE IN GRAPHQL

As the GraphQL application grows, keeping **schema definitions,** **queries**, **mutations**, and **resolvers** in a single file can make the code difficult to maintain. 
To solve this problem, we can modularize the GraphQL codebase.

Instead of keeping everything inside **graphql/index.js**, we create separate modules for each feature.
In this example, all User-related GraphQL code is placed inside **graphql/user/**.

The User module is divided into separate files:

`typeDefs.js` → Contains the User type definition.

`queries.js` → Contains User-related **query definitions**.
`mutations.js` → Contains User-related **mutation definitions**.
`resolvers.js` → Contains the actual business logic for **queries** and **mutations**.
`index.js` → Combines all User-related GraphQL components into one module.
The main `graphql/index.js` file then imports the User module and combines its definitions and resolvers with the main GraphQL schema.
This approach follows the idea of **separation of concerns**, where each file has a specific responsibility.

If we later add features such as **Product**, **Order**, or **Customer**, we can create separate modules for them:

graphql/
├── index.js
├── user/
│   ├── index.js
│   ├── typeDefs.js
│   ├── queries.js
│   ├── mutations.js
│   └── resolvers.js
├── product/
│   ├── index.js
│   ├── typeDefs.js
│   ├── queries.js
│   ├── mutations.js
│   └── resolvers.js
└── order/
    ├── index.js
    ├── typeDefs.js
    ├── queries.js
    ├── mutations.js
    └── resolvers.js

This makes the codebase cleaner, easier to navigate, easier to debug, and easier to scale.
The important idea is: each feature owns its GraphQL **types**, **queries**, **mutations**, and **resolvers**, while the main `graphql/index.js` acts as the place where all modules are combined.

### Practical Implementation
`📂 database/connection.js`
```javascript
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
```javascript
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

`📂 graphql/user/typeDefs.js`
```javascript
const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        email: String!
    }
`;

module.exports = { typeDefs };
```

`📂 graphql/user/queries.js`
```javascript
const queries = `
    fetchAllUsers: [User]
    fetchUser(id: ID!): User
`;

module.exports = { queries };
```

`📂 graphql/user/mutations.js`
```javascript
const mutations = `
    createUser(name: String!, age: Int!, email: String!): Boolean!
    updateUser(id: ID!, name: String!, age: Int!, email: String!): Boolean!
    deleteUser(id: ID!): Boolean!
`;

module.exports = { mutations };
```

`📂 graphql/user/resolvers.js`
```javascript
const User = require("../../models/userModel");

// Queires
const queries = {
    // Fetch all users
    fetchAllUsers: async () => {
        const users = await User.find({}).limit(5);
        return users;
    },

    // Fetch user by ID
    fetchUser: async (_, { id }) => {
        const user = await User.findById(id);
        return user;
    }
};

// Mutations
const mutations = {
    // Create user
    createUser: async (_, { name, age, email }) => {
        const user = await User.create({ name, age, email });
        return user ? true : false;
    },

    // Update user
    updateUser: async (_, { id, name, age, email }) => {
        const user = await User.findByIdAndUpdate(id, { $set: { name, age, email } });
        return user ? true : false;
    },

    // Delete user
    deleteUser: async (_, { id }) => {
        const user = await User.findByIdAndDelete(id);
        return user ? true : false;
    }    
};

// Resolver contain queries and mutations
const resolvers = { queries, mutations };

module.exports = { resolvers };
```

`📂 graphql/user/index.js`
```javascript
const { typeDefs } = require("./typeDefs");
const { queries } = require("./queries");
const { mutations } = require("./mutations");
const { resolvers } = require("./resolvers");

// Grouped with User
const User = { typeDefs, queries, mutations, resolvers };

module.exports = { User };
```

`📂 graphql/index.js`
```javascript
const { ApolloServer } = require("@apollo/server");
const { User } = require("./user");

// Initialize apollo server
const apolloServer = new ApolloServer({
    typeDefs: `
        ${User.typeDefs}

        type Query {
            ${User.queries}
        }

        type Mutation {
            ${User.mutations}
        }
    `,

    resolvers: {
        Query: {
            ...User.resolvers.queries
        },

        Mutation: {
            ...User.resolvers.mutations
        }
    }
});

module.exports = apolloServer;
```

`📂 app.js`
```javascript
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
```javascript
require("dotenv").config();
const app = require("./app");
const connectDB = require("./database/connection");
const apolloServer = require("./graphql");
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
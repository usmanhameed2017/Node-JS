# GRAPHQL MUTATION

**Mutation** is used to modify data in GraphQL. Just like REST APIs use POST, PUT/PATCH, and DELETE for changing data, GraphQL uses Mutation for these operations.

Mutation can be used to:

- Create new data

- Update existing data

- Delete data

Mutations are defined inside the type Mutation block in the GraphQL schema. Each mutation has a name, arguments, and a return type.

**Example**:

```javascript
type Mutation {
    createUser(name: String!, age: Int!, email: String!): Boolean!
}
```

Here:

- **createUser** → mutation name

- **name, age, email** → arguments required by the mutation
 
- **Boolean**! → return type

The actual logic of the mutation is written inside the Mutation resolver. 
The resolver receives the arguments sent by the client and performs the required operation in the database.

## Practical Implementation
`📂 graphql/server.js`
```javascript
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
            hasNextPage: Boolean!
            hasPrevPage: Boolean!
            prevPage: Int
            nextPage: Int
        }   

        type Query {
            fetchUsers(page: Int, limit: Int): UserPagination
            fetchUserById(_id: ID!): User
        }

        type Mutation {
            createUser(name: String!, age: Int!, email: String!): Boolean!
            updateUser(id: ID!, name: String!, age: Int!, email: String!): Boolean!
            deleteUser(id: ID!): Boolean!
        }
    `,
    
    resolvers: {
        Query: {
            fetchUsers: async (_, { page = 1, limit = 10 }) => {
                const users = await User.aggregatePaginate([
                    { $match: {} },

                    // Projection
                    { $project: { _id: 1, name: 1, age: 1, email: 1 } }
                ], { page, limit });
                return users;
            },

            fetchUserById: async (_, { _id }) => {
                const user = await User.findById(_id).lean()
                return user;
            }
        },

        Mutation: {
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
            },            
        }
    }
});

module.exports = apolloServer;
```
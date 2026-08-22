# GRAPHQL RESOLVER PARAMETERS

A GraphQL resolver function receives **four parameters** automatically:

```javascript
resolver(parent, args, context, info)
```

These parameters provide different types of information to the resolver.

### Resolver Parameters

| Parameter | Contains | Common Use |
|---|---|---|
| `parent` | Data returned by the parent resolver | Resolving nested fields |
| `args` | Arguments provided to the query or mutation | Accessing client input |
| `context` | Shared server-side data | Authentication, `req`, `res`, user, etc. |
| `info` | Detailed information about the current GraphQL execution | Advanced query/execution handling |

---

### Parent Parameter

The `parent` parameter contains the **value returned by the parent resolver**.

For a root-level `Query` or `Mutation` resolver, there is usually no parent resolver, so `parent` is commonly:

```javascript
undefined
```

Example:

```javascript
const resolvers = {
    Query: {
        fetchUser: async (parent, args, context, info) => {
            console.log(parent);
        }
    }
};
```

Output:

```text
undefined
```

However, `parent` becomes important when resolving **nested fields**.

Consider this schema:

```graphql
type User {
    name: String!
    age: Int!
}

type Query {
    fetchUser: User
}
```

The resolver for `fetchUser` returns a User object:

```javascript
const resolvers = {
    Query: {
        fetchUser: async () => {
            return {
                name: "Usman",
                age: 26
            };
        }
    },

    User: {
        name: (parent) => {
            console.log(parent);
            return parent.name;
        }
    }
};
```

When the `User.name` resolver executes, its `parent` contains:

```javascript
{
    name: "Usman",
    age: 26
}
```

The flow is:

```text
Query.fetchUser
       ↓
Returns User object
       ↓
User.name resolver
       ↓
parent = User object
```

Therefore, the main purpose of `parent` is to provide the **result of the previous/parent resolver to the current resolver**.

**Practical point:** `parent` is mostly ignored in root-level resolvers, but it becomes very useful when working with nested GraphQL types.

---

### Args Parameter

The `args` parameter contains **all arguments provided to the current GraphQL field**.

For example, suppose the schema contains:

```graphql
type Query {
    fetchUser(id: ID!): User
}
```

The client sends:

```graphql
query {
    fetchUser(id: "123")
}
```

The resolver can access the argument through `args`:

```javascript
fetchUser: async (parent, args) => {
    console.log(args);
}
```

Output:

```javascript
{
    id: "123"
}
```

If the field has multiple arguments:

```graphql
type Query {
    fetchUsers(page: Int, limit: Int): UserPagination
}
```

And the client sends:

```graphql
query {
    fetchUsers(page: 2, limit: 10)
}
```

Then `args` contains:

```javascript
{
    page: 2,
    limit: 10
}
```

We can also destructure the arguments directly:

```javascript
fetchUsers: async (parent, { page, limit }) => {
    console.log(page);
    console.log(limit);
}
```

Default values can also be assigned:

```javascript
fetchUsers: async (parent, { page = 1, limit = 10 }) => {
    // ...
}
```

If the client does not provide `page` or `limit`, the resolver will use `1` and `10`.

The important concept is:

```text
GraphQL Schema
      ↓
Field arguments
      ↓
Client provides values
      ↓
args parameter
      ↓
Resolver
```

For example:

```graphql
fetchUsers(page: Int, limit: Int): UserPagination
```

allows the resolver to access:

```javascript
fetchUsers: async (parent, { page, limit }) => {

}
```

Therefore, `args` is mainly used to access **input values provided to a query or mutation through field arguments**.

---

### Context Parameter

The `context` parameter is a **shared object provided by the GraphQL server configuration**.

It allows us to make server-side information available to resolvers.

If no custom context is configured, it can commonly appear as:

```javascript
{}
```

For example:

```javascript
fetchUsers: async (parent, args, context) => {
    console.log(context);
}
```

Output:

```javascript
{}
```

We can add our own values to the context through the GraphQL server configuration.

For example:

```javascript
expressMiddleware(apolloServer, {
    context: async () => {
        return {
            message: "Hello GraphQL"
        };
    }
});
```

Now the resolver can access that value:

```javascript
fetchUsers: async (parent, args, context) => {
    console.log(context.message);
}
```

Output:

```text
Hello GraphQL
```

The basic concept is:

```text
GraphQL Configuration
        ↓
     Context
        ↓
     Resolver
```

The context object is particularly useful because the same context can be made available to different resolvers during a GraphQL request.

---

### Context with Express Request and Response

One of the most useful applications of `context` in an **Express + Apollo Server** application is making the Express `req` and `res` objects available inside GraphQL resolvers.

When using Express middleware integration:

```javascript
app.use(
    "/graphql",
    expressMiddleware(apolloServer, {
        context: async ({ req, res }) => {
            return {
                req,
                res
            };
        }
    })
);
```

Now the resolver can access them through `context`:

```javascript
fetchUsers: async (parent, args, context) => {

    console.log(context.req);
    console.log(context.res);

}
```

We can also destructure them:

```javascript
fetchUsers: async (parent, args, { req, res }) => {

    console.log(req);
    console.log(res);

}
```

This becomes particularly useful for authentication.

For example, if `cookie-parser` is configured:

```javascript
app.use(cookieParser());
```

Then the resolver can access cookies through the Express request:

```javascript
fetchUsers: async (parent, args, { req }) => {

    const token = req.cookies.accessToken;

    // Verify token...

}
```

The complete flow becomes:

```text
Browser Request
      ↓
Express Request (req)
      ↓
Apollo GraphQL Context
      ↓
GraphQL Resolver
      ↓
req.cookies / req.headers / req.user
```

We can also put other server-side information into the context, such as the authenticated user:

```javascript
context: async ({ req, res }) => {
    return {
        req,
        res,
        user: req.user
    };
}
```

Then:

```javascript
fetchUsers: async (parent, args, { user }) => {

    console.log(user);

}
```

The context can therefore be used to pass **shared server-side information** to resolvers.

Common examples include:

```text
req
res
authenticated user
database connection
services
loaders
authentication information
```

**Important:** `context` is different from `args`.

- `args` → Input provided by the GraphQL client.
- `context` → Server-side information provided by the GraphQL configuration.

---

### Info Parameter

The `info` parameter contains **detailed information about the current GraphQL field, schema, query, and execution process**.

Example:

```javascript
fetchUsers: async (parent, args, context, info) => {

    console.log(info);

}
```

The `info` object can contain information such as:

```javascript
{
    fieldName,
    fieldNodes,
    returnType,
    parentType,
    path,
    schema,
    fragments,
    operation,
    variableValues
}
```

The `info` parameter is generally used for **advanced GraphQL use cases**. In normal CRUD resolvers, it is often not required.

---

### Info — fieldName

`fieldName` tells us the name of the field whose resolver is currently executing.

```javascript
info.fieldName
```

For:

```graphql
query {
    fetchUsers {
        docs {
            name
        }
    }
}
```

The `fetchUsers` resolver will have:

```javascript
info.fieldName
```

as:

```text
fetchUsers
```

---

### Info — parentType

`parentType` tells us the GraphQL type that owns the current field.

```javascript
info.parentType
```

For a root-level query:

```graphql
type Query {
    fetchUsers: UserPagination
}
```

the parent type will be:

```text
Query
```

---

### Info — returnType

`returnType` tells us the GraphQL return type of the current field.

For example:

```graphql
fetchUsers: UserPagination!
```

then:

```javascript
info.returnType
```

contains information about the `UserPagination!` GraphQL type.

---

### Info — fieldNodes

`fieldNodes` contains the AST representation of the current field selection.

For example, the client sends:

```graphql
query {
    fetchUsers {
        docs {
            name
            email
        }
        totalDocs
    }
}
```

GraphQL internally parses this query into an **Abstract Syntax Tree (AST)**.

`info.fieldNodes` provides access to the AST node representing the current field.

This can be useful when building advanced functionality that needs to inspect **which fields the client requested**.

---

### Info — operation

`operation` contains information about the current GraphQL operation.

For example:

```graphql
query FetchUsers {
    fetchUsers {
        docs {
            name
        }
    }
}
```

The operation information can be accessed through:

```javascript
info.operation
```

It contains information such as the operation type:

```text
query
mutation
subscription
```

as well as the operation's selection set and other details.

---

### Info — schema

The `schema` property provides access to the current GraphQL schema:

```javascript
info.schema
```

It can be used in advanced scenarios where we need to inspect GraphQL types, fields, directives, or other schema-level information.

---

### Complete Resolver Parameters Example

A resolver can receive all four parameters:

```javascript
const resolvers = {

    Query: {

        fetchUsers: async (parent, args, context, info) => {

            console.log("Parent:", parent);

            console.log("Args:", args);

            console.log("Context:", context);

            console.log("Field Name:", info.fieldName);

            // Database logic...

            return users;
        }
    }
};
```

The overall concept can be visualized as:

```text
resolver(parent, args, context, info)

        │
        ├── parent
        │     └── Data returned by the parent resolver
        │
        ├── args
        │     └── Arguments provided to the current field
        │
        ├── context
        │     └── Shared server-side data
        │
        └── info
              └── GraphQL execution and schema information
```

### Quick Summary

- **`parent`** → Contains the value returned by the parent resolver. It is usually ignored in root-level resolvers but is important for nested resolvers.
- **`args`** → Contains the arguments provided to the current query or mutation field.
- **`context`** → Contains shared server-side data provided through GraphQL configuration. It is commonly used for authentication, `req`, `res`, authenticated users, services, and other shared resources.
- **`info`** → Contains detailed information about the current GraphQL field, query, schema, and execution process. It is mainly useful for advanced GraphQL functionality.
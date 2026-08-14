# GRAPHQL NESTED TYPES

In GraphQL, real-world data often contains nested objects and arrays of objects.

GraphQL handles this by defining separate types for each object and then connecting those types together.

**Example**:

Customer
   │
   └── orders[]
          │
          └── Order
                │
                └── products[]
                       │
                       └── Product

Here, Customer contains an array of Order objects, and each Order contains an array of Product objects.

The important part is that we can use one custom type inside another.

products: [Product]

This means products is an array of Product objects.

Similarly:

orders: [Order] means orders is an array of Order objects.

---

### Querying Nested Data

The client can decide exactly which fields it wants from each nested level:

```javascript
query {
    fetchCustomer {
        id
        name
        orders {
            orderId
            products {
                name
                qty
                price
            }
        }
    }
}
```

GraphQL then traverses the nested structure and returns the requested fields:

```javascript
{
    "data": {
        "fetchCustomer": {
            "id": "2017",
            "name": "Usman Hameed",
            "orders": [
                {
                    "orderId": "#uv917",
                    "products": [
                        {
                            "name": "Iphone",
                            "qty": 2,
                            "price": 100000
                        }
                    ]
                }
            ]
        }
    }
}
```

## Practical Implementation
`📂 graphql/server.js`
```javascript
const { ApolloServer } = require("@apollo/server");

// Initialize apollo server
const apolloServer = new ApolloServer({
    typeDefs: `
        type Product {
            name: String!
            qty: Int!
            price: Float!
        }

        type Order {
            orderId: ID!
            products: [Product]
        }
            
        type Customer {
            id: ID!
            name: String!
            orders: [Order]
        }

        type Query {
            fetchCustomer: Customer!
        }
    `,
    
    resolvers: {
        Query: {
            fetchCustomer: () => {
                const customer = {
                    id: "2017",
                    name: "Usman Hameed",
                    orders: [
                        { 
                            orderId: "#uv917",
                            products: [{ name: "Iphone", qty: 2, price: 100000 }] 
                        },
                        { 
                            orderId: "#V9t21",
                            products: [{ name: "Tablet", qty: 1, price: 50000 }, { name: "Earbuds", qty: 3, price: 30000.02 }], 
                        }                        
                    ]
                };
                return customer;
            }         
        }
    }
});

module.exports = apolloServer;
```
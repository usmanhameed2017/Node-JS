# DATA MODELING

- `Data modeling` in Mongoose is the process of designing the structure of stored data to ensure efficient organization, retrieval, and relationships between documents.

- Since MongoDB is a NoSQL database, it uses documents (JSON-like objects) instead of tables, making data modeling flexible.

### Schema Design

- In Mongoose, schemas define the structure of a document, including field types, validation rules, and default values.

### Embedded VS Referenced Data

- `Embedded Data (Denormalization)`: Stores related data inside the same document for fast access.

- `Referenced Data (Normalization)`: Stores related data in separate collections and links them using ObjectIDs.

### Indexes & Performance

- Indexing helps improve query speed by efficiently locating documents.

### One-to-Many & Many-to-Many Relationships
  
- Relationships between documents can be handled using arrays or references.

### Scalability & Flexibility

- Unlike SQL databases, MongoDB allows a dynamic schema, meaning documents in the same collection can have different structures.

## PRACTICAL IMPLEMENTATION

#### models/admin.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    name:{
        type:String,
        required:true
    },
    email:{
        type:String,
        required:true,
        unique:true,
        lowercase:true
    },
    password:{
        type:String,
        required:true
    }
});

// Model
const Admin = model("Admin", schema);

module.exports = Admin;
```

#### models/product.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    name:{
        type:String,
        required:true
    },
    price:{
        type:Number,
        required:true,
    },
    createdBy:{
        type:Schema.Types.ObjectId,
        ref:"Admin"
    },
    category:{
        type:Schema.Types.ObjectId,
        ref:"Category"
    }
}, { timestamps:true });

// Model
const Product = model("Product", schema);

module.exports = Product;
```

#### models/category.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    name:{
        type:String,
        required:true
    },
    createdBy:{
        type:Schema.Types.ObjectId,
        ref:"Admin"
    }
}, { timestamps:true });

// Model
const Category = model("Category", schema);

module.exports = Category;
```

#### models/user.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    name:{
        type:String,
        required:true
    },
    age:{
        type:Number,
        required:true
    },
    email:{
        type:String,
        required:true,
        unique:true,
        lowercase:true
    },
    password:{
        type:String,
        required:true
    }
});

// Model
const User = model("User", schema);

module.exports = User;
```

#### models/order.js
```javascript
const { Schema, model } = require("mongoose");

// Schema
const schema = new Schema({
    orderBy:{
        type:Schema.Types.ObjectId,
        ref:"User"
    },
    items:[
        {
            productId:{
                type:Schema.Types.ObjectId,
                ref:"Product"
            },
            quantity:{
                type:Number,
                required:true
            }
        }
    ],
    price:{
        type:Number,
        required:true
    }
}, { timestamps:true });

// Model
const Order = model("Order", schema);

module.exports = Order;
```
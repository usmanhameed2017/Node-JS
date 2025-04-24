# 📘 SERVER-SIDE PAGINATION

## 📄 What is Pagination?

**Pagination** is the process of dividing a large set of data into smaller chunks (pages), so that the user can navigate through data more easily without being overwhelmed.  
Instead of displaying all data at once (which could be thousands of records), we show a limited number like 10, 20, or 50 records per page.

**🔹 Example:**  
Google search results show only 10 results per page — that’s pagination.

---

## 🔁 What is Server-Side Pagination?

**Server-side pagination** means that the data is paginated on the server before being sent to the client (browser).  
Instead of sending the full dataset and filtering on the frontend, the client sends a request like:

> "Give me page 3 with 10 items per page"

And the server responds with just those 10 records.

---

## ✅ Why Use Server-Side Pagination?

### 1. 🚀 Performance Boost
Only a small portion of data is sent to the client, which makes the page load faster.

### 2. 📊 Efficient for Large Datasets
Works really well when you have thousands or millions of records (like in admin panels, reports, etc.)

### 3. 📱 Reduces Browser Load
The client device (especially mobiles) doesn't have to process a huge dataset.

### 4. 🔍 Works with Search/Filters
Server can apply search, filters, sorting, etc., and return only the needed page.

---

💡 **Tip:**  
Use server-side pagination when dealing with large datasets to ensure faster performance and better user experience.

### ⚙ Basic Pagination Setup

#### Install `mongoose-paginate-v2` package.
```javascript
npm install mongoose-paginate-v2
```

---

- Import it inside models file
```javascript
import mongoosePaginate from 'mongoose-paginate-v2'
```

---

- Inject `mongoosePaginate` plugin into schema.
```javascript
userSchema.plugin(mongoosePaginate);
```

---

Paginate method will return these values

- `docs` (Array) - Array of documents
- `totalDocs` (Number) - Total number of documents in collection that match a query
- `limit` (Number) - Limit that was used.
- `page` - Current page number.
- `hasPrevPage` (Bool) - Availability of prev page.
- `hasNextPage` (Bool) - Availability of next page.
- `prevPage` (Number) - Previous page number if available or NULL
- `nextPage` - Next page number if available or NULL
- `pagingCounter` (Number) - The starting index/serial/chronological number Of first document in current page. (Eg: if page-2 and limit=10, then pagingCounter will be 11)
- `totalPages` (Number) - Total number of pages.
- `offset` (Number) - Only if specified or default page / offset values were used

## PRACTICAL IMPLEMENTATION

#### models/user.js
```javascript
const mongoose = require("mongoose");
const mongoosePaginate = require("mongoose-paginate-v2");

// Schema
const userSchema = new mongoose.Schema({
    name:{
        type:String,
        required:true,
        trim:true
    },
    age:{
        type:Number
    },
    email:{
        type:String,
        unique:true,
        required:true,
        lowercase:true,
        trim:true
    },
    gender:{
        type:String,
        required:true,
        enum:["Male", "Female"]
    }
}, { timestamps:true });

// Inject plugin
userSchema.plugin(mongoosePaginate);

// Model
const User = mongoose.model("User", userSchema);

module.exports = User;
```

---

#### index.js
```javascript
const express = require("express");
const connectDB = require("./database/connection");
const User = require("./models/user");
const app = express();

// Database connection
connectDB("mongodb://localhost:27017", "pagination_db");

// Serve static files
app.use("/public", express.static(__dirname + "/public"));
app.use("/css", express.static(__dirname + "/node_modules/bootstrap/dist/css"));
app.use("/js", express.static(__dirname + "/node_modules/bootstrap/dist/js"));

// Enable EJS Template Engine
app.set("view engine", "ejs");

// Route - Home
app.get("/", (request, response) => response.render("home"));

// Route - Users
app.get("/users", async (request, response) => {

    // Destructure query string parameters 
    const { page = 1, limit = 10 } = request.query; // Page initial value will be 1 and limit will be 10. 

    // Database query
    const result = await User.paginate({}, { page, limit });

    // If page size is greater than total pages
    if(page > result.totalPages) return response.render("notFound");

    // Render users
    return response.render("users", { result });

});

// Start server
app.listen(8000, () => console.log("Server is started and running at: http://localhost:8000")); 
```

---

#### views/user.ejs
```html
<div class="container mt-1">

    <!-- Table -->
    <div class="row">
        <div class="col-md-8 mx-auto">
            <div class="table-responsive">
                <table class="table table-boldered table-hover table-striped text-center">
                    <thead>
                        <tr>
                            <th> Sr.No </th>
                            <th> Name </th>
                            <th> Age </th>
                            <th> Email </th>
                            <th> Salary </th>
                        </tr>
                    </thead>
                    <tbody>
                        <% if(result?.docs && Array.isArray(result?.docs) && result?.docs?.length > 0) { %>
                            <% result?.docs?.map((user, index) => { %>
                                <tr>
                                    <td> <%= result.pagingCounter++ %> </td>
                                    <td> <%= user.name %> </td>
                                    <td> <%= user.age %> </td>
                                    <td> <%= user.email %> </td>
                                    <td> <%= user.gender %> </td>
                                </tr>
                           <% }); %>
                        <% } else { %>
                            <tr>
                                <td colspan="5" class="text-center"> No data found </td>
                            </tr>
                        <% } %>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- Pagination -->
    <div class="row mt-2">
        <div class="col-md-5 mx-auto">
            <nav aria-label="Page navigation example">
                <ul class="pagination">

                    <!-- Previous Button -->
                    <li class="page-item <%= !result.hasPrevPage && "disabled" %>">
                        <a class="page-link" href="users?page=<%= result.prevPage %>">Previous</a>
                    </li>

                    <!-- Total Buttons -->
                    <% for(let i = 1; i <= result.totalPages; i++) { %>
                        <li class="page-item <%= result.page == i && "active" %>">
                            <a class="page-link" href="users?page=<%= i %>"> <%= i %> </a>
                        </li>
                    <% } %>

                    <!-- Next Button -->
                    <li class="page-item <%= !result.hasNextPage && "disabled" %>">
                        <a class="page-link" href="users?page=<%= result.nextPage %>">Next</a>
                    </li>

                </ul>
            </nav>
        </div>
    </div>

    <!-- Total Records Count (10 of 50) -->
    <div class="row">
        <div class="col-md-1 mx-auto">
            <strong class="text-secondary"> <%= result.pagingCounter - 1 %> of <%= result.totalDocs %> </strong>
        </div>
    </div>    

</div>
```
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
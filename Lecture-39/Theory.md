# UPDATE DOCUMENTS IN COLLECTION

- To update an existing document we can use two methods.
1. `updateOne()`
2. `updateMany()`

### updateOne()

- The `updateOne()` method will update the first document that is found matching the provided query.

#### Syntax
```shell
db.collection_name.updateOne(
    { key: "value" }, -- Query object/Filter (Works as same as `where` clause in MYSQL)
    { $set: { key: "updated_value", key: "updated_value" } }
)

db.users.updateOne(
    { name: "Usman" },
    { $set: { age:27 } }
)
```

### updateMany()

- The `updateMany()` method will update all the documents that is matching with the provided query object.

#### Syntax
```shell
db.collection_name.updateMany(
    { key: "value" }, -- Query object/Filter (Works as same as `where` clause in MYSQL)
    { $set: { key: "updated_value", key: "updated_value" } }
)

db.users.updateMany(
    { designation: "Manager" },
    { $set: { designation: "Co-Founder" } }
)
```

- The first parameter is a `query object` to define which document or documents should be updated.

- The second parameter is an object defining the `updated data`.

> Note: Always use `object id` to update `single-json object` record.
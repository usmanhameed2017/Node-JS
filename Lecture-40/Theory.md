# DELETE DOCUMENTS FROM COLLECTION

- We can delete documents by using the methods `deleteOne()` or `deleteMany()`.

- These methods accept a query object. The matching documents will be deleted.

- ### deleteOne()

- The `deleteOne()` method will delete the first document that matches the provided query object.

#### Syntax
```shell
db.collection_name.deleteOne(
    { key:"value" }
);
```

### deleteMany()

- The `deleteMany()` method will delete all documents that match the provided query object.

#### Syntax
```shell
db.collection_name.deleteMany(
    { key:"value" }
);
```

> Note: To delete all the records from a collection, pass an empty object `{}` to `deleteMany()` method.

#### Syntax
```shell
db.collection_name.deleteMany({});
```
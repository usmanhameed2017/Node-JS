# SELECT DOCUMENTS FROM COLLECTION

- There are 2 methods to find and select data from a MongoDB collection, `find()` and `findOne()`.

### find()

- This method accepts a query object. If left empty, all documents will be returned.

#### Syntax
```shell
db.collection_name.find(
    { key:"value" }
);
```

### findOne()

- This method accepts a query object. If left empty, it will return the first document it finds.

- To select only one document, we can use the `findOne()` method.

#### Syntax
```shell
db.collection_name.findOne(
    { key:"value" }
);
```

### Projection

- Both find methods accept a second parameter called projection.

- This parameter is an Object that describes which fields to include in the results.

#### Syntax
```shell
db.collection_name.find(
    { key:"value" },
    { fieldOne:1, fieldTwo:1 }
);
```

- To disable _id column, specify 0 to _id field.

#### Syntax
```shell
db.users.find({}, { _id:0, name:1, username:1 }
);
```
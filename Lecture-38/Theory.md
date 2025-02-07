# INSERT DOCUMENTS IN COLLECTION

- There are two methods to insert documents into a collection.
1. `insertOne()`
2. `insertMany()`

### insertOne()

- To insert a single document, use the `insertOne()` method.

- This method inserts a single object into the collection.

#### Syntax 
```shell
db.collection_name.insertOne({ name:"Usman", age:24, email:"usman@gmail.com" }) 
```

### insertMany()

- To insert multiple documents at once, use the `insertMany()` method.

- This method inserts an `array of objects` into the collection.

#### Syntax
```shell
db.collection_name.insertMany([
    { name:"Usman", age:21, email:"usman@gmail.com" },
    { name:"Bilal", age:24, email:"bilal@gmail.com" },
    { name:"Raheel", age:23, email:"raheel@gmail.com" }
])
```
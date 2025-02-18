# IMPORTANT METHODS USED WITH FIND METHOD

### count()

- MongoDB's `count()` method is a powerful tool for retrieving the `number of documents in a collection` that match a specified query.
 
#### Syntax:
```shell
db.collection.find({}).count()
```

### sort()

- In MongoDB, the `sort()` method is used to specify the order in which the query returns matching documents from a collection.

- It allows users to arrange documents in either ascending `1` or descending `-1` order based on the values of one or more fields.

#### Syntax:
```shell
db.collection.find({}).sort({ name:1 })
```

### limit()

- The `limit()` method in MongoDB is a powerful tool used to control the `number of documents` returned in a query result.

- It is particularly beneficial when working with large collections as it allows for the restriction of result set sizes thereby improving performance and reducing client load.

#### Syntax:
```shell
db.collection.find({}).limit(3)
```

### skip()

- In MongoDB, the `skip()` method skips the number of documents specified in the parameter.

- The `skip()` method is mainly used with the `limit()` method to optimize large datasets and improve performance, such as in `server-side pagination`.

#### Syntax:
```shell
db.collection.find({}).limit(10).skip(10)
```

### projection()

- We can use find methods with `projection()` to return data set with selective fields.

#### Syntax:
```shell
db.collection.find({}).projection({ name:1, age:1, email:1, _id:0 })
```

> Note: You can also perform method chaining to get an absolutely specific records.

#### Syntax:
```shell
db.collection.find({ designation:"Manager" }).sort({ name:1 }).limit(5).skip(10).projection({ name:1, designation:1, _id:0 })
```
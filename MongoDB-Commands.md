- Create new database or activate an existing database
```shell
use database_name
```

- Show current selected database.
```shell
db
```

- Show all databases.
```shell
show dbs
```

- Delete database.
```shell
db.dropDatabase()
```

- Create a new collection. 
```shell
db.createCollection("collection_name")
```

- Show all collections in current selected database.
```shell
show collections
```

- Change collection name.
```shell
db.old_collection_name.renameCollection("new_collection_name")
```

- Delete a collection.
```shell
db.collection_name.drop()
```

- View all commands to manipulate database.
```shell
db.help()
```

- View all commands to manipulate collection.
```shell
db.collection_name.help()
```

- Insert one document into a collection.
```shell
db.collection_name.insertOne({ name:"Usman", age:24, email:"usman@gmail.com" })
```

- Insert many documents into a collection.
```shell
db.collection_name.insertMany([
    { name:"Usman", age:21, email:"usman@gmail.com" },
    { name:"Bilal", age:24, email:"bilal@gmail.com" },
    { name:"Raheel", age:23, email:"raheel@gmail.com" }
])
```

- Update single document in a collection.
```shell
db.users.updateOne(
    { _id: ObjectId('67a55470c58c6b3b90576cb4') },
    { $set: { designation: "Manager" } }
)
```

- Update multiple documents in a collection.
```shell
db.users.updateMany(
    { age: 18 },
    { $set: { category: "Adult" } }
)
```

- Delete single document in a collection.
```shell
db.users.deleteOne(
    { _id: ObjectId('67aa8ec388515978b42724a2') }
)
```

- Delete multiple documents in a collection.
```shell
db.users.deleteMany(
    { _id: ObjectId('67aa8ec388515978b42724a2') }
)
```

- Delete all documents in a collection.
```shell
db.users.deleteMany({})
```
- Create new database or activate an existing database
```javascript
use database_name
```

- Show current selected database.
```javascript
db
```

- Show all databases.
```javascript
show dbs
```

- Delete database.
```javascript
db.dropDatabase()
```

- Create a new collection. 
```javascript
db.createCollection("collection_name")
```

- Show all collections in current selected database.
```javascript
show collections
```

- Change collection name.
```javascript
db.old_collection_name.renameCollection("new_collection_name")
```

- Delete a collection.
```javascript
db.collection_name.drop()
```

- View all commands to manipulate database.
```javascript
db.help()
```

- View all commands to manipulate collection.
```javascript
db.collection_name.help()
```

- Insert one document into a collection.
```javascript
db.collection_name.insertOne({ name:"Usman", age:24, email:"usman@gmail.com" })
```

- Insert many documents into a collection.
```javascript
db.collection_name.insertMany([
    { name:"Usman", age:21, email:"usman@gmail.com" },
    { name:"Bilal", age:24, email:"bilal@gmail.com" },
    { name:"Raheel", age:23, email:"raheel@gmail.com" }
])
```
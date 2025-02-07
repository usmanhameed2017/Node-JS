- Show all databases.
```shell
show dbs
```

- Create new database or activate an existing database
```shell
use database_name
```

- Show current selected database.
```shell
db
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
# CREATING DATABASE & COLLECTIONS

#### Mongosh

- MongoDB Shell is the quickest way to connect, configure, query, and work with your MongoDB database.

- It acts as a `command-line` client of the MongoDB server.

- It is an environment for interacting with MongoDB deployments in Atlas , locally, or on another remote host.

### Creating Database

- Command: `use database_name`

- The `use` command is used to create a new database or activate an existing database.

- Now, open Mongosh and click on `Add New Connection` button.

- Set your server name and click on `Save & Connect` button.

- Open cmd and enter command `use database_name` to create a database.

- Type `show dbs` to view all databases.

- Type `db` to view current activated database.

- The keyword `db` refers to the current selected database.

### Creating Collections

- Command:  `db.createCollection("collection_name")`.

- Type `show collections` to view all collections within selected database.

- Type `db.old_collection.renameCollection("new_collection")` to rename your collection.

-

-

-

-
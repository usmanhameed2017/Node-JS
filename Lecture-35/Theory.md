# BSON IN MONGODB

- BSON (Binary JSON) is a `binary representation` of JSON like documents.

- JSON data converts into `BSON` and then store into MongoDB.

- It is designed to be efficient in terms of both storage space and speed of scan and traversal.

- It was developed in 2009 by MongoDB.

- It is a `binary-encoded serialization` of JSON documents.

- BSON compresses and optimizes data storage and retrieval.

- It is designed to handle large data sets and complex nested structures efficiently, making it a core feature in MongoDB.

- BSON is just a binary JSON (a superset of JSON with some more data types, most importantly `binary byte array`).

- JSON files are written in text format whereas, BSON files are written in binary.

- JSON is a human-readable format that doesn't require parsing. But, BSON needs to be parsed as they are machine-generated and not human-readable.

- JSON has a specific set of data types; string, boolean, number for numeric data types, array, object, and null.

- Unlike JSON, BSON offers additional data types such as `bindata` for binary data, `decimal128` for numeric.

- BSON is faster than JSON.
# STREAMS

- Node.js streams are a way to handle large amount of data efficiently by processing it in small chunks instead of loading everything into memory at once.

- Streams allow data to be read or written in small chunks rather than loading the entire data at once.

- Instead of waiting for the entire file to load, streams process data in small parts.

- They improve performance and memory usage, especially for large files or real-time data.

- Streams use events like `data`, `end`, `error`, and `finish` to handle data flow.

- You can connect streams together using `.pipe()`, like reading from a file and writing to another file in one step.

### Stream Types

- `Readable Streams` – Used for reading data (e.g. `fs.createReadStream()` for reading files).

- `Writable Streams` – Used for writing data (e.g. `fs.createWriteStream()` for saving files).

- `Duplex Streams` – Can both read and write data (e.g. TCP sockets).

- `Transform Streams` – Used for modifying data as it passes through (e.g. compression).

## PRACTICAL IMPLEMENTATION

#### index.js
```javascript
app.get("/", (request, response) => {
    // Create read stream
    const stream = fs.createReadStream("./sample.txt","utf-8");
    stream.on("data", (chunk) => response.write(chunk));
    stream.on("end", () => response.end());
});
```

- You can create a ZIP file using the `zlib` module in Node.js by combining streams with pipes. 

- The `zlib.createGzip()` method compresses data, and you can use streams to read a file, compress it, and write it to a new .gz file efficiently.

#### index.js
```javascript
app.get("/zip", (request, response) => {
    fs.createReadStream("./sample.txt","utf-8").pipe(zlib.createGzip().pipe(fs.createWriteStream("./sample.zip")));
    return response.end("Success");
});
```
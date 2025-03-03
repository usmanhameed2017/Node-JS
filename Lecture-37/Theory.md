# CLUSTERS

- Clusters of `Node.js` processes can be used to run multiple instances of Node.js that can distribute workloads among their application threads.

- `Node.js` clusters allow you to take advantage of multi-core processors by running multiple instances of your Node.js application in parallel.

- This helps improve performance and handle more requests efficiently.

- It enables running multiple `Node.js` processes to handle multiple requests simultaneously.

- Node.js runs on a `single-threaded` event loop, so clustering helps utilize multiple CPU cores for better performance.

- The cluster module balances incoming requests across worker processes and work as a load balancer.

- To check your system's total CPU cores, import `os` module and check the length of `cpus` method.

> Note: Cluster in Node.js basically balances the work load so that we can handle more traffic.

#### index.js
```javascript
const os = require("os");
console.log(os.cpus().length);
```

- Scale Node.js application using clusterization.

#### index.js
```javascript
const express = require("express");
const cluster = require("cluster");
const os = require("os");

// Express app
const app = express();

// Route
app.get("/", (request, response) => response.send(`Hello from a server! ✨ ${process.pid}`));

// Total CPU
const totalCPUS = os.cpus().length;

// Clusterization
if(cluster.isPrimary)
{
    for(let i = 1; i <= totalCPUS; i++)
    {
        cluster.fork();
    }
}
else
{
    // Start server
    app.listen(8000, () => console.log("Server is started and running at http://localhost:8000"));
}
```
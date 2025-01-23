# SYNCHRONOUS AND ASYNCHRONOUS

### Synchronous Programming
• `Synchronous` is a sequential & blocking architecture, so the execution of each operation depends on completing the one before it.

• Each task requires an answer before moving on to the next iteration.

• `Sync` is a single-thread, so only one operation or program will run at a time.

• `Sync` is a blocking model and it will only send the server one request at a time and wait for that request to be answered by the server.

### Asynchronous Programming
• `Asynchronous` is a non-blocking architecture, so the execution of one task isn't dependent on another.

• Tasks can run simultaneously (parallel).

• `Async` is a multi-thread, which means operations or programs can run in parallel.

• `Async` is non-blocking, which means it will send multiple requests to a server.

• `Async` increases throughput because multiple operations can run at the same time.

###### Note: Node JS follows `Asynchronous`, but Node JS also have `synchronous methods` to perform synchronous specific tasks.
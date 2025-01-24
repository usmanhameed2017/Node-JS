# NODE JS

• When we talk about `Node JS`, we can create applications using JavaScript which we can run every where not just on web browser.

• When JavaScript Launched it was mainly for the websites.

• So if you want to make your website interactive in that case we can use JavaScript.

• Also we can run JavaScript on Web Browsers.

• But now we can run JavaScript on desktop, mobile app and as a server as well.

• That's why we can build Backend Applications in Node JS.

• So that means we can use Node JS as a Backend server.

• But, javascript follows `single thread` approach.

## Node JS Web Server

• Node js web server works as a `single-thread`, but its single thread will not be blocked.

• It means the single thread of Node.js will not be blocked, nor it stay in pending state.

### Handle Multiple Request

• To handle multiple request with single thread, Node.js have two concepts:
1. Non-Blocking I/O
2. Asynchronous

##### Non-Blocking I/O

• `Single-thread` will recieve a request from a client and assign it to a `worker`.

##### Asynchronous

• After worker sends the request to an another server, then the server generates a response and sends it back to the client `Asynchronously`.

• And for Asynchronous, we use `callback` functions.

• There is a library called `libuv` specifically designed for Node.js. 

• Node use libuv for `workers` concept.

• Libuv provides you the concept of Non-Blocking I/O.

• Libuv is built in `C Language`.

• C language uses `System Kernel`.

• System Kernel has `Multiple Threads`.

• It means in Node JS, we are not using multiple threads but behind the scenes Kernel is implementing multiple threads.

• That's what make Node JS Fast And Flexible.
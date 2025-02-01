# INTRODUCTION TO EXPRESS JS

- `Express JS` is a minimal and flexible `Node.js` web application framework that provides a robust set of features for web applications.

- It simplifies the process of building `web servers` and `APIs` by providing a set of tools and `middleware` to handle various tasks such as `routing`, `request handling`, and `response management`.

### Key Features of Express JS

- Middleware Support
- Routing
- Template Engines
- Error Handling
- Static File Serving
- RESTful API Development

#### Middleware Support

- Express.js uses middleware functions to handle requests. Middleware can perform tasks such as `logging`,  `authentication`, and `parsing request bodies`.

#### Routing

- It provides a powerful routing system that allows you to define routes for different HTTP methods `(GET, POST, PUT, DELETE)` easily.

#### Template Engines

- Express can integrate with various template engines (like EJS, Pug, Handlebars) to render dynamic HTML pages.

#### Error Handling

- Express has a built-in `error handling` mechanism that allows you to define custom error-handling middleware.

#### Static File Serving

- It can serve static files (like images, CSS, JavaScript) easily, which is essential for web applications.

#### RESTful API Development

- Express is widely used to build RESTful APIs, providing a `clean and efficient` way to handle `HTTP requests and responses`.

### How Express JS Works with Node JS

- Node.js is known for its non-blocking, event-driven architecture, which is excellent for building `efficient`, `scalable network applications`.

- However, coding directly in Node JS can become verbose and complex, especially when handling routes and server logic.

- Express.js comes in as a `lightweight framework` that abstracts some of these complexities and provides a simpler interface.

- Since it runs on top of Node.js, Express can use all of Node.js capabilities while simplifying common tasks.

### When to Use Express JS

- `RESTful APIs`: To create backend services quickly.

- `Web Apps`: To build server-side rendered applications.

- `Middleware-Heavy Applications`: When you need to handle tasks such as logging, authentication, and error handling.

#### Conclusion

- Express.js simplifies the process of creating a `robust web application` using Node.js.

- With its various features and flexibility, it is widely used for `server-side applications` and `APIs`.

- As you gain more experience, you can explore its extensive middleware support and routing capabilities to build more complex applications.

## INSTALLATION OF EXPRESS JS

- Open new terminal and write `npm init` command to create a package.json file for your application.

- Now, write command to install `Express.js`.

```
npm i express
```

- To install Express temporarily and not add it to the dependencies list:

```
npm i express --no-save
```
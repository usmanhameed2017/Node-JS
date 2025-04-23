# EXPRESS.JS RESPONSE METHODS

Express.js provides various response methods to handle and send responses to the client. Below are the most commonly used ones:

---

### 1. response.send()
Sends a plain text, HTML, or any other content type response to the client.
```javascript
app.get('/', (request, response) => {
    response.send('Welcome to Express!');
});
```

---

### 2. response.json()
Sends a JSON response. Automatically sets `Content-Type` to `application/json`.
```javascript
app.get('/api', (request, response) => {
    response.json({ message: "Hello API" });
});
```

---

### 3. response.jsonp()
Sends a JSON response with JSONP support (for cross-domain requests).
```javascript
app.get('/data', (request, response) => {
    response.jsonp({ message: "This is JSONP" });
});
```

---

### 4. response.redirect()
Redirects the request to another URL.
```javascript
app.get('/google', (request, response) => {
    response.redirect('https://www.google.com');
});
```

---

### 5. response.render()
Renders a view template using a view engine like EJS, Pug, etc.
```javascript
app.get('/home', (request, response) => {
    response.render('home.ejs');
});
```

---

### 6. response.download()
Transfers a file as an attachment for download.
```javascript
app.get('/download', (request, response) => {
    response.download('./files/sample.pdf');
});
```

---

### 7. response.sendFile()
Sends a file as a response to the client.
```javascript
app.get('/', (request, response) => {
    response.sendFile(__dirname + '/public/index.html');
});
```

---

### 8. response.end()
Ends the response process without any data (optional data can be sent).
```javascript
app.get('/end', (request, response) => {
    response.end('Goodbye!');
});
```

---

### 9. response.sendStatus()
Sets the HTTP response status code and sends its string representation.
```javascript
app.get('/not-found', (request, response) => {
    response.sendStatus(404); // Sends "Not Found"
});
```

---

### 10. response.headersSent
A boolean that returns true if the headers have already been sent to the client.
```javascript
app.use((request, response, next) => {
    console.log(response.headersSent); // true or false
    next();
});
```

---

### 11. response.set()
Sets response headers (single or multiple).
```javascript
app.get('/set-header', (request, response) => {
    response.set('Content-Type', 'text/plain');
    response.send('Header is set');
});
```

---

### 12. response.get()
Returns the value of a response header previously set.
```javascript
app.get('/get-header', (request, response) => {
    response.set('Content-Type', 'application/json');
    const contentType = response.get('Content-Type');
    response.send(`Content-Type is ${contentType}`);
});
```

---
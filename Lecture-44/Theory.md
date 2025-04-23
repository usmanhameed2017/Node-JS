# EXPRESS JS REQUEST PROPERTIES AND METHODS

## 🧾 Properties

### 1. `request.body`
Contains the parsed body of POST/PUT request. Requires body-parser or express.json().
```javascript
app.post('/login', (request, response) => {
    console.log(request.body); // { username: 'john', password: '123' }
    response.send('Logged In');
});
```

### 2. `request.params`
Used to access route parameters.
```javascript
app.get('/user/:id', (request, response) => {
    console.log(request.params); // { id: '123' }
    response.send('User ID: ' + request.params.id);
});
```

### 3. `request.query`
Used to access query string parameters.
```javascript
app.get('/search', (request, response) => {
    console.log(request.query); // { q: 'nodejs' }
    response.send('Search Query: ' + request.query.q);
});
```

### 4. `request.file`
Access single uploaded file (using multer).
```javascript
app.post('/upload', upload.single('profile'), (request, response) => {
    console.log(request.file); // Info about uploaded file
    response.send('File uploaded');
});
```

### 5. `request.files`
Access multiple uploaded files.
```javascript
app.post('/upload', upload.array('photos', 3), (request, response) => {
    console.log(request.files); // Array of uploaded files
    response.send('Files uploaded');
});
```

### 6. `request.cookies`
Access cookies (requires cookie-parser middleware).
```javascript
app.get('/', (request, response) => {
    console.log(request.cookies); // { user: 'john' }
    response.send('Cookies accessed');
});
```

### 7. `request.hostname`
Returns the hostname from the request.
```javascript
app.get('/', (request, response) => {
    console.log(request.hostname); // localhost
    response.send('Hostname accessed');
});
```

### 8. `request.ip`
Returns the IP address of the client.
```javascript
app.get('/', (request, response) => {
    console.log(request.ip); // ::1 or 127.0.0.1
    response.send('IP accessed');
});
```

### 9. `request.ips`
If `trust proxy` is enabled, returns an array of IPs.
```javascript
app.set('trust proxy', true);
app.get('/', (request, response) => {
    console.log(request.ips); // ['clientIP', 'proxyIP']
    response.send('IPs accessed');
});
```

### 10. `request.method`
Returns the HTTP method used.
```javascript
app.get('/', (request, response) => {
    console.log(request.method); // GET
    response.send('Method accessed');
});
```

### 11. `request.originalUrl`
Returns the full URL including query string.
```javascript
app.get('*', (request, response) => {
    console.log(request.originalUrl); // /path?q=value
    response.send('Original URL');
});
```

### 12. `request.path`
Returns the URL path without query string.
```javascript
app.get('*', (request, response) => {
    console.log(request.path); // /path
    response.send('Path accessed');
});
```

### 13. `request.protocol`
Returns the request protocol (http/https).
```javascript
app.get('/', (request, response) => {
    console.log(request.protocol); // http
    response.send('Protocol accessed');
});
```

### 14. `request.secure`
Returns true if HTTPS.
```javascript
app.get('/', (request, response) => {
    console.log(request.secure); // false if http
    response.send('Secure? ' + request.secure);
});
```

### 15. `request.route`
Returns the route object that matched the request.
```javascript
app.get('/route', (request, response) => {
    console.log(request.route); // { path: '/route', ... }
    response.send('Route matched');
});
```

---

## 🧰 Methods

### 1. `request.accepts()`
Checks if the request accepts a certain content type.
```javascript
app.get('/', (request, response) => {
    console.log(request.accepts('html')); // true
    response.send('Accepts HTML? ' + request.accepts('html'));
});
```

### 2. `request.get()`
Get value from a request header.
```javascript
app.get('/', (request, response) => {
    console.log(request.get('User-Agent')); // Browser info
    response.send('Header accessed');
});
```

### 3. `request.is()`
Check Content-Type of request body.
```javascript
app.post('/', (request, response) => {
    console.log(request.is('json')); // true or false
    response.send('Type checked');
});
```

### 4. `request.range()`
Parses `Range` header (used for partial downloads).
```javascript
app.get('/', (request, response) => {
    console.log(request.range(1000)); // { start: x, end: y }
    response.send('Range header parsed');
});
```
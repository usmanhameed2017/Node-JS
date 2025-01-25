# URL MODULE IN NODE JS

### URL

- A URL `Uniform Resource Locator` is the address of a unique resource on the internet.

- It is one of the key mechanisms used by browsers to retrieve published resources, such as `HTML pages`, `CSS documents`, `images`, and so on.

- In theory, each valid URL points to a unique resource.

###### https://www.example.com/category/shirts?name=Usman&age=24#section7

- The segments of the above URL are:
>1. `https`: Protocol 
>2. `www.example.com`: Domain/Host name
>3. `category`: Path 
>4. `category/shirts`: Nested Path 
>5. `?`: Query String Separator
>6. `?name=Usman&age=24`: Query String Parameters 
>7. `#section7`: Fragment 

- A hash sign `#` in a URL is referred to as a fragment.

- Historically, URL fragments have been used to automatically set the browser's scroll position to a predefined location in the web page.

- In that sense, if a URL refers to a `document`, then the `fragment` refers to a `specific subsection` of that document.

### The Built-In URL Module In Node JS

- The URL module splits up a web address into a readable parts.

- The Node JS URL module provides us with utilities for parsing of the URL.

- `Parsing` is the process of transforming an unstructured data into a tree or parse tree (structured data) that's easier to read, understand and use.

- The URL string is a structured string that contains multiple segments.

#### main.js
```javascript
// Import URL Module
const url = require("url");

// Base URL
const base_url = "https://www.example.com/category/shirts?name=Usman&age=24#section7";

// Parse URL
const parsed_url = url.parse(base_url, true);

// Print In Console
console.log(parsed_url);
console.log("Protocol: ", parsed_url.protocol);
console.log("Host: ", parsed_url.host);
console.log("Port Number: ", parsed_url.port);
console.log("Path Name: ", parsed_url.pathname);
console.log("Query Parameters: ", parsed_url.query);
console.log("Search: ", parsed_url.search);
console.log("Hash: ", parsed_url.hash);
console.log("Complete Path: ", parsed_url.path);
console.log("Slashes: ", parsed_url.slashes);
console.log("Auth: ", parsed_url.auth);
console.log("Complete URL: ", parsed_url.href);
```

- Implement `URL Module` with `HTTP Module`.

#### main.js
```javascript
// Import Modules
const url = require("url");
const http = require("http");

// Create A Server
http.createServer((request, response) => {

    // Write A Response
    response.write("Response From A Server");

    // Parse Request URL
    const parse_url = url.parse(request.url, true);

    // Print Request URL Object
    console.log("Request URL Object", parse_url);

    // End Response
    response.end();
}).listen(8000, () => console.log("Server is started running at http://localhost:8000")); 
```
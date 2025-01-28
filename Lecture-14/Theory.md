# PATH MODULE IN NODE JS

### PATH MODULE

- The path module in Node JS is a core module that offers utilities for managing file and directory paths.

- It helps handle and transform paths across different operating systems, ensuring platform independence.

- The module includes methods for `joining`, `resolving`, and `normalizing` paths, among other common file system tasks.

### METHODS IN PATH MODULE

- extname
- join
- normalize
- basename
- dirname
- parse
- resolve
- isAbsolute

#### Basename

- The `basename()` method is used to get the `filename portion` of a path to the file.

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Complete Path
const url = "E:\\Usman\\NodeJS\\Lectures\\Lecture01\\Theory.md";

// Extract & Print Filename
console.log(path.basename(url));
```

#### Extname

- `extname()` method returns the `extension` of the file in a path.

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Filename
const url = "Theory.md";

// Extract & Print File Extension
console.log(path.extname(url));
```

#### Dirname

- `dirname()` method is used to get the `directory name` of the given path.

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Complete Path
const url = "E:\\Usman\\NodeJS\\Lectures\\Lecture01\\Theory.md";

// Extract & Print Complete Directory
console.log(path.dirname(url));
```

#### Join

- `join()` method is used to join a number of path segments.

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Join Path Segments
console.log(path.join("Usman", "Node-JS", "Lectures", "Lecture01", "Theory.md"));
```

#### Resolve

- `resolve()` method resolves a sequence of paths into an absolute path (Complete path).

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Complete Absolute Path
console.log(path.resolve("Lectures", "Lecture01", "Theory.md"));
```

#### Normalize

- The `normalize()` method is used to normalize the given path. Normalization resolves the (.) and (..) segments of the path to their correct form.

#### main.js
```javascript
// Import Path Module
const path = require("path");

const url = "E:\\\\Usman\\\\Node-JS\\\\Lectures\\\\Lecture-01\\\\Theory.md"; // Extra slashes

// Normalize Path
console.log(path.normalize(url));
```

#### Parse

- The `parse()` method is used to return an object whose properties represent the given path. The method returns the following properties:
> 1. root (root name)
> 2. dir (directory name)
> 3. base (filename with extension)
> 4. ext (only extension)
> 5. name (only filename)

#### main.js
```javascript
// Import Path Module
const path = require("path");

const url = "E:\\Usman\\Node-JS\\Lectures\\Lecture-01\\Theory.md";

// Parsing
const parse_url = path.parse(url);

// Print Properties Individually
console.log("Root name: ", parse_url.root);
console.log("Directory name: ", parse_url.dir);
console.log("File name with extension: ", parse_url.base);
console.log("File extension only: ", parse_url.ext);
console.log("File name only: ", parse_url.name);
```

#### isAbsolute

- The `isAbsolute()` method is used to check whether the given path is an absolute path or not.

- An absolute path is defined as a path that contains the complete details needed to locate a file.

#### main.js
```javascript
// Import Path Module
const path = require("path");

// Complete Absolute Path
const url = "E:\\Usman\\Node-JS\\Lectures\\Lecture-01\\Theory.md";

// Check if the path is absolute
if(path.isAbsolute(url) === true)
{
    console.log("The path is absolute");
}
else
{
    console.log("The path is relative");
}
```

> `Absolute Path`: `E:\\Usman\\Node-JS\\Lectures\\Lecture-01\\Theory.md`

> `Relative Path`: `Usman\\Node-JS\\Lectures\\Lecture-01\\Theory.md`
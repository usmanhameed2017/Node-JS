# MODULES IN NODE JS

• In Node.js, any file that consists of JavaScript code in a file ending with `.js` is a module.

• A module can contain definitions of functions, classes, objects, or variables that can be referenced or used in another JavaScript file.

• When your application starts getting larger, maintaining a single file becomes a difficult task.

• It is easy to get lost in the codebase and lose track of what a particular piece of code is doing.

• To make it easier to maintain, reuse and organize code, you need to split the code into multiple files.

• This process is called `modularization`.

• Each module contains functions or classes that handle a specific functionality.

• Functions in one module can be imported and called in other modules saving you from having to copy function definitions into the other files.

• A module can be edited or debugged separately making it easier for you to add or remove new features.

• Modules are created in Node.js by creating a JavaScript file.

• Every time you create a new file with `.js` extension, it becomes a module.

• Let's write our first module. We will start by creating two functions to do simple calculations.

###### Note: To export your variables, functions or classes, you need to write `module.exports` at the end of your file and pass your variables, functions or classes inside curly braces. We will use the `require` keyword at the top of the file to import functions, variables or classes from another module. The result of require is then stored in a variable which is used to invoke the functions using the dot notation.

### Calculation.js
```javascript

// Addition
function add(a, b)
{
    return a + b;
}

// Subtraction
function subtract(a, b)
{
    return a - b;
}

module.exports = { add, subtract };
```

### main.js
```javascript

// Import functions from Calculation
const lib = require("./Calculation.js");

// Call functions
console.log("Addition result is: " + lib.add(20, 10));
console.log("Subtraction result is: " + lib.subtract(20, 10));
```

• 2nd way of exporting functions or variables:

### Calculation.js
```javascript
module.exports = {

    // Addition
    add: function(a, b)
    {
        return a + b;
    },

    // Subtraction
    subtract: function(a, b)
    {
        return a - b;
    },

    // Variable
    email: 'usman@gmail.com'
};
```

• Third way of exporting functions or variables:
### Calculation.js
```javascript
// Addition
module.exports.add = function(a, b)
{
    return a + b;
}

// Subtraction
module.exports.subtract = function(a, b)
{
    return a - b;
}

// Variable
module.exports.email = 'usmanhameed1790@gmail.com';
```

#### Destructuring Syntax
• You can also use the destructuring syntax to unpack the properties of the object returned by require and store them in variables.

### main.js
```javascript
const { add, subtract, email } = require("./Calculation.js");

console.log("Addition result is: " + add(20, 10));
console.log("Subtraction result is: " + subtract(20, 10));
console.log("Email is " + email);
```
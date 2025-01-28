# MODULE WRAPPER FUNCTION IN NODE JS

- In Node.js, when we create a module and add code to it, Node.js automatically wraps the entire file's code in a `Module Wrapper Function` behind the scenes.

- The `Module Wrapper Function` creates a `block scope`, ensuring that variables or functions with the same name do not conflict.

- Additionally, the Module Wrapper Function provides some seemingly global variables as its parameters, including `exports`, `require`, `module`, `__filename`, and `__dirname`. These variables are accessible in every file and can be used as needed based on your work requirements.

#### Module Wrapper Function Syntax:
```javascript
(function(exports, require, module, __filename, __dirname) {
    // Module code
    let username = "usman@123";
    console.log(username);
})();
```

- Behind the scenes, Node.js does not run our code directly, it wraps the entire code inside a `Module Wrapper Function` before execution. This is why, in every file, you have access to seemingly global variables like `exports`, `require`, `module`, `__filename`, and `__dirname`.

- Every module in Node.js is wrapped in a wrapper function before being loaded, which provides module scope.

### Use Of Module Wrapper Function in NodeJS

- It provides some global-looking variables that are specific to the module, such as: `module`, `exports`, `__filename`, `__dirname`.

- The `module` and `exports` object can be used to export values from the module.

- The variables like `__filename` and `__dirname`, tell us the module's absolute filename and its directory path.

### Benefits Of Module Wrapper Function

- This saves us from having to worry about conflicting variables or functions.

- There is proper encapsulation and code reusability.
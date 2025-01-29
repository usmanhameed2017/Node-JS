# CALLBACK PATTERN IN NODE JS

### Callback Function

- A callback is a function passed as an argument to another function.

- This technique allows a function to call another function.

#### main.js
```javascript
// Function 1
function Show1(name)
{
    console.log(`Hello ${name}`);
}

// Function 2
function Show2(Show1) // Here Show1 is a callback function, and Show2 is a High Order Function
{
    const name = "Usman Hameed";
    Show1(name); // Call Function 1
}

// Call Function 2
Show2(Show1);
```

- Another example of callback function is:

#### main.js
```javascript
// Define Greet Function
function Greet(name, callback)
{
    console.log(`Hello ${name}`);
    callback();
}

// Call Greet Function 
Greet("Usman", () => {
    console.log("This is a callback function");
});
```

- A callback function can run after another function has finished.

- A callback function can also be returned as values from other functions.

### Types Of Callback
1. Asynchronous Callbacks
2. Synchronous Callbacks

#### Asynchronous Callback Example
```javascript
fs.readFile("info.txt", "utf-8", (error, data) => {
    console.log(data);
});
```

#### Synchronous Callback Example
```javascript
// Function 1
function Show1(name)
{
    console.log(`Hello ${name}`);
}

// Function 2
function Show2(Show1) // Here Show1 is a callback function, and Show2 is a High Order Function
{
    const name = "Usman Hameed";
    Show1(name); // Call Function 1
}

// Call Function 2
Show2(Show1);
```

- A callback function which is executed immediately is called a `Synchronous Callback`.

- A callback function that is often used to continue code execution after an Asynchronous operation has completed in a High Order Function is called `Asynchronous Callback`.

- `Asynchronous Callbacks` are used to delay the execution of a function until a particular event has occurred such as; reading file, fetching data from a server, updating a data etc.

- Node JS have an `Asynchronous` nature to prevent blocking of execution.
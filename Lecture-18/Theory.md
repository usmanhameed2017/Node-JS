# EVENTS MODULE IN NODE JS

- The events module allows us to work with events in Node JS.

### Most Common Events
1. On Click
2. On Double Click
3. On Mouse Over
4. On Mouse Out
5. On Submit
6. On Change
7. On Reset
8. On Keyup

- Node.js is perfect for `event-driven` applications.

#### main.js
```javascript
// Import Events Module
const events = require("events");

// Create An Object Of `Event Emitter` class
const eventEmitter = new events.EventEmitter();

// Attach A Callback Function To An Action
eventEmitter.on("order-pasta", () => {
    console.log("Order has been received and we started preparing your meal!");
});

// Register Event
eventEmitter.emit("order-pasta");
```

- We can also pass an arguments to a callback function.

#### main.js
```javascript
// Import Events Module
const events = require("events");

// Create An Object Of `Event Emitter` class
const eventEmitter = new events.EventEmitter();

// Attach A Callback Function To An Action With Parameters
eventEmitter.on("order-pasta", (size, topping) => {
    console.log(`Order has been received and we started preparing your ${size} pasta with extra topping of ${topping}`);
});

// Register Event With Arguments
eventEmitter.emit("order-pasta", "Medium", "Black Olives");
```

- We can also attach multiple `Event Handlers` to a single `Event`.

#### main.js
```javascript
// Import Events Module
const events = require("events");

// Create An Object Of `Event Emitter` class
const eventEmitter = new events.EventEmitter();

// Attach Event Handler 01 
eventEmitter.on("order-pasta", (size, topping) => {
    console.log(`Order has been received and we started preparing your ${size} pasta with extra topping of ${topping}`);
});

// Attach Event Handler 02
eventEmitter.on("order-pasta", (size) => {
    if(size === "Large")
    {
        console.log("Serving a complementary Red Bull");
    }
    else if(size === "Medium")
    {
        console.log("Serving a complementary Soft Drink")
    }
    else
    {
        console.log("While we are preparing your meal, you can go to our website and provide us with a prompt feedback! :)");
    }
});

// Register Event
eventEmitter.emit("order-pasta", "Medium", "Black Olives");
```

- An event is an `action` that has happened in our application we can respond to.

- By using the events module, we can create our own custom events and respond to those custom events in a `non-blocking` manner.

- Node.js has a built-in module, called `events`, where you can create, fire, and listen for your own events.
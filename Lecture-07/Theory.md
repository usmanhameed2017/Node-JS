# NODEMON & ITS ALTERNATE
### Nodemon
• Nodemon is a tool that support `hot reload` in Node.js backend applications.

• This means you don't need to close and restart your applicatidns for changes to take effect.

• The server automatically restarts and applies changes when a file is modified and saved.

### Nodemon Installation Command
```
npm i nodemon -g
```

• After nodemon installation, type command in terminal: `nodemon fileName.js`.

• If nodemon gives an error for disable scripts, then follow these steps:
###### 1. Open Windows PowerShell with Run as Administrator
###### 2. Run this command: `get-executionpolicy` to check current execution policy in your system.
###### 3. Run this command: `set-executionpolicy unrestricted` to unrestrict it.

### Alternate of Nodemon
• Type this command in terminal to watch live changes in your file/module: `node --watch fileName.js`.

• With the introduction of the built-in feature in Node.js `18.11`, we can achieve hot reload without need of nodemon.

• Node.js 18.11 brings a new feature called `--watch` that allows hot reloading without relying on nodemon.
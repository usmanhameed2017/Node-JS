# TYPES OF MODULES IN NODE JS
1. Local Modules
2. Core Modules
3. Third-Party Modules.

## Local Modules
• These are the modules that you can create yourself and use them in your application.

• To import a local module, you have to use the `require()` method with the local module's name passed as the argument.

## Core Modules
• These are modules that come with Node.js by default.

• You do not have to download them in your project.

• Some of the most popular and frequently used core modules are `FS (File System)`, `OS (Operating System)`, `PATH` etc.

• To import a core module, you have to use the `require()` method with the core module's name passed as the argument.

### Global And Non-Global Core Modules
• `console` is a global core module because we don't need to import it before use.

• `FS (FileSystem)` is non-global core module it means we have to import it before use.

## Third-Party Modules (External Modules)
• Third-party modules are modules that are downloaded with a package manager such as npm.

• These modules are usually stored in the `node modules` folder.

• You can install third-party modules globally or locally in your project.

• Examples of third party modules are `express`, `mongoose`, `react` etc.

### Command to install third-party module `locally`
```
npm i module_name
```

### Command to install third-party module `globally`
```
npm i module_name -g
```

###### Note: `-g` stands for globally.

• To import a third-party module, you have to use the require() method that takes the third-party module's name as an argument.
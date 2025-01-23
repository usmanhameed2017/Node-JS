# FS MODULE
• Fs module is used to provide `file handling`.

• File handling refers to the process by which data is stored and retrieved from `files` using a program.

• The Node.js file system module allows you to work with the file system on your computer.

• To include the File System module, use the `require()` method.

### Common Use For The File System Module:

1. Read files
2. Create files
3. Update files
4. Delete files
5. Rename files

### Create Files Using FS Module

• The File System module has methods for creating new files:

#### `writeFileSync`
```javascript
const fs = require("fs");

fs.writeFileSync("info.txt", "My name is Usman Hameed");
```

#### `writeFile`
```javascript
const fs = require("fs");
fs.writeFile("info.txt", "My name is Usman Hameed", (error) => {
    if(error) console.log(error);
    console.log("File has been created & data has been inserted successfully!");
});
```




































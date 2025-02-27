# FS MODULE
- Fs module is used to provide `file handling`.

- File handling refers to the process by which data is stored and retrieved from `files` using a program.

- The Node.js file system module allows you to work with the file system on your computer.

- To include the File System module, use the `require()` method.

### Common Use For The File System Module:

1. Read files
2. Create files
3. Update files
4. Delete files
5. Rename files
6. Copy files

### Create Files Using FS Module

- The File System module has methods for creating new files:

#### writeFileSync
```javascript
const fs = require("fs");
fs.writeFileSync("info.txt", "My name is Usman Hameed");
```

#### writeFile
```javascript
const fs = require("fs");
fs.writeFile("info.txt", "My name is Usman Hameed", (error) => {
    if(error) console.log(error);
    console.log("File has been created & data has been inserted successfully!");
});
```

### Read Files Using FS Module

- The File System module has methods for reading files:

#### readFileSync
```javascript
const fs = require("fs");
const data = fs.readFileSync("info.txt", "utf-8");
console.log(data);
```

#### readFile
```javascript
const fs = require("fs");
fs.readFile("info.txt", "utf-8", (error, data) => {
    if(error) console.log(error);
    console.log(data);
});
```

### Update Files Using FS Module

- The File System module has methods for updating files:

#### appendFileSync
```javascript
const fs = require("fs");
fs.appendFileSync("info.txt", " and age is 24");
```

#### appendFile
```javascript
const fs = require("fs");
fs.appendFile("info.txt", " and age is 24", (error) => {
    if(error) console.log(error);
    console.log("Data has been updated successfully");
});
```

### Delete Files Using FS Module

- To delete a file with the File System module, use the `unlink()` or `unlinkSync()` method.

#### unlinkSync
```javascript
const fs = require("fs");
fs.unlinkSync("info.txt");
```

#### unlink
```javascript
const fs = require("fs");
fs.unlink("info.txt", (error) => {
    if(error) console.log(error);
    console.log("File has been deleted successfully!");
});
```

### Rename Files Using FS Module

- To rename a file with the File System module, use the `rename()` or `renameSync()` method.

#### renameSync
```javascript
const fs = require("fs");
fs.renameSync("info.txt", "bio.txt");
```

#### rename
```javascript
const fs = require("fs");
fs.rename("info.txt", "bio.txt", (error) => {
    if(error) console.log(error);
    console.log("File has been renamed successfully!");
});
```

###### Note: We can also move file by using `rename()` or `renameSync()` method, providing a specific directory path.

### Copying Files Using FS Module

- To copy a file with the File System module, use the `cp()` or `cpSync()` method.

- The `fs.cp()` and `fs.cpSync()` method copy the specified file.

#### cpSync
```javascript
const fs = require("fs");
fs.cpSync("info.txt", "infoCopy.txt");
```

#### cp
```javascript
const fs = require("fs");
fs.cp("info.txt", "infoCopy.txt", (error) => {
    if(error) console.log(error);
    console.log("The copy of a file has been generated!");
});
```

## Use of FS Module In Real World Applications

- The fs module is essential for performing `file system operations` in Node. js applications.

- Whether you need to read configuration files, write `log files`, the fs module provides the necessary tools to interact with the file system efficiently.

- To check if a file exists, you can use the `existsSync` method from the fs module. 

- `existsSync` method takes a file path as a parameter and returns a boolean value indicating whether the file exists or not.

#### existsSync
```javascript
if(fs.existsSync("./info.txt"))
{
    console.log("File exists");
}
else
{
    console.log("File does not exist");
}
```
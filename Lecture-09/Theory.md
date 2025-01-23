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

• The File System module has methods for reading files:

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

• The File System module has methods for updating files:

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

• To delete a file with the File System module, use the `unlink()` or `unlinkSync` method.

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






































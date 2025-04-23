# MULTER - FILE UPLOAD MIDDLEWARE IN NODE.JS

- `Multer` is a Node.js middleware used for handling `multipart/form-data`, which is used to upload files.

- It is commonly used with `Express.js` and allows us to upload single or multiple files to the server.

- It helps you upload files like images, PDFs, etc.

- It stores files in memory or on disk like `uploads/` folder.

- It enables us to set file name, folder location, file type check, and file limits easily.

### Install Multer
```javascript
npm install multer
```

### Basic Setup With Disk-Storage

```javascript
const multer = require('multer');

// Define storage
const storage = multer.diskStorage({
    destination: function (request, file, cb) 
    {
        return cb(null, 'uploads/'); // Folder to store files
    },
    filename: function (request, file, cb) 
    {
        const uniqueName = `${Date.now()}-${file.originalname}`;
        return cb(null, uniqueName); // Unique filename
    }
});

// Initialize multer with storage
const upload = multer({ storage: storage });

module.exports = upload;
```

### Upload Single File (1 File Only)

> Use upload.single('fieldname') — where `fieldname` is the name  of input file name in your HTML form.

```javascript
app.post('/upload', upload.single('profile_image'), (request, response) => {
    const profile_image_path = request.file?.path;
    // Save profile image url/path to database
    console.log('Profile Image Path:', profile_image_path); 
    response.send('Single file uploaded');
});
```

#### HTML
```javascript
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="profile_image" />
    <button type="submit"> Upload </button>
</form>
```

### Upload Multiple Files (From Different Fields)

> Use upload.fields() when uploading files from different fields in the same form.

```javascript
app.post('/upload', upload.fields([{ name: 'profile_image', maxCount: 1 }, { name: 'resume', maxCount: 1 }]), (request, response) => {
    const profile_image_path = request.files?.profile_image?.[0]?.path;
    const resume_path = request.files?.resume?.[0]?.path;

    // Save these paths to database
    console.log('Profile Pic:', profile_image_path);
    console.log('Resume:', resume_path);
    response.send('Multiple files uploaded from different fields');
});
```

#### HTML
```javascript
<form action="/upload" method="POST" enctype="multipart/form-data">
    <input type="file" name="profile_image" />
    <input type="file" name="resume" />
    <button type="submit"> Upload </button>
</form>
```

### Upload Multiple Files (From Same Field)

> Use upload.array('fieldname', maxCount) for uploading multiple files using the same field.

```javascript
app.post('/gallery', upload.array('photos', 5), (request, response) => {
    const photoPaths = request.files?.map(file => file.path);
    // Save photoPaths array to database
    console.log('Uploaded Photo Paths:', photoPaths);    
    response.send('Multiple files uploaded from the same field');
});
```

#### HTML
```javascript
<form action="/gallery" method="POST" enctype="multipart/form-data">
    <input type="file" name="photos" multiple />
    <button type="submit"> Upload </button>
</form>

```

> Note: Always use `enctype="multipart/form-data"` in forms. Also, make sure the `uploads/` folder exists or create it before using multer.
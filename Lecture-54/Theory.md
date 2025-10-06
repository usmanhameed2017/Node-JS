# 📬 NODEMAILER

- Nodemailer is a Node.js module to send emails easily using SMTP.

- It works with email services like Gmail, Yahoo, Outlook, etc.

- Generate App Password from [here](https://myaccount.google.com/apppasswords).

---

### 📦 Installation
```bash
npm install nodemailer
```

---

### 🚀 Sending Email With Gmail
```javascript
const express = require("express");
const nodemailer = require("nodemailer");

const app = express();

// Configure transporter
const transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
        user: 'your_email@gmail.com',
        pass: 'your_password' // ⚠️ If 2FA is ON, use App Password
    }
});

// Configure mail options
const mailOptions = {
    from: 'your_email@gmail.com',
    to: 'recipient@example.com',
    subject: 'Test Email',
    text: 'Hello! This is a test email sent using Nodemailer.'
}

// Send mail
app.get("/send-mail", async (request, response) => {
    try 
    {
        const info = await transporter.sendMail(mailOptions);
        console.log("Response:", info.response);
        return response.status(200).send("Email has been sent successfully..!");
    } 
    catch(error) 
    {
        console.log("Error:", error.message);
        return response.status(500).send("Failed to send email..!");
    }
});

// Start server
app.listen(8000, () => console.log("Server is started and running at http://localhost:8000"));
```

---

### ⚠️ If You Use 2-Step Verification (2FA)

- Gmail will not accept normal password.

- Use an **App Password** instead.

- Generate App Password from: [https://myaccount.google.com/apppasswords].

---

### HTML Emails

- Use the **html** key instead of **text** in `mailOptions`.
```javascript
html: '<h1> Hello User </h1> <p> This is an HTML email. </p>'
```

--- 

### Attach Files

- Use the **attachments** key in `mailOptions`.

- **attachments** key is an array.
```javascript
attachments: [
    {
        filename: 'demo.txt',
        path: './demo.txt'
    }
];
```

---

### ✅ Conclusion

- Nodemailer makes email sending super easy.

- service: `gmail` is a shortcut for Gmail SMTP.

- Don’t forget to generate App Password from [here](https://myaccount.google.com/apppasswords).
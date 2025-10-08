# EXPRESS MINIFY HTML TERSER

### ⚡ Optimizing HTML Output with **express-minify-html-terser**

- When building production-ready Express.js applications, performance and load speed are critical. One effective optimization is minifying HTML responses — removing unnecessary spaces, comments, and line breaks from the rendered output.

- That’s where express-minify-html-terser comes in. It’s a lightweight middleware that automatically minifies HTML sent by your Express server before sending it to the client.

- Normally, your HTML files contain:
```html
<!DOCTYPE html>
<html>
    <head>
        <title>My App</title>
    </head>
    <body>
        <h1>Welcome To My Website</h1>
    </body>
</html>
```

- After minification, this becomes:
```html
<!DOCTYPE html><html><head><title>My App</title></head><body><h1>Welcome to my website</h1></body></html>
```

- This reduces file size and **improves load speed**, especially on low-bandwidth connections.

---

### ⚙️ Installation
```javascript
npm install express-minify-html-terser
```

---

### 💻 Basic Setup in Express
```javascript
const express = require("express");
const minifyHTML = require("express-minify-html-terser");

// Express app
const app = express();

// Inject middleware
app.use(minifyHTML({
        override:true,
        htmlMinifier: {
            removeComments:true,
            collapseWhitespace:true,
            collapseBooleanAttributes:true,
            removeAttributeQuotes:true,
            removeRedundantAttributes:true,
            removeScriptTypeAttributes:true,
            removeStyleLinkTypeAttributes:true,
            minifyCSS:true,
            minifyJS:true
        }
    })
);

// Set up EJS template engine
app.set("view engine", "ejs");
app.set("views", "./views");

// Route
app.get("/", (request, response) => {
    response.render("index", { title: "Synchrix Chat" });
});

// Start server
app.listen(8000, () => console.log("Server running on port 8000 🚀"));
```

---

> In the above snippet code:

- The middleware intercepts all **HTML responses**.

- It minifies them using **Terser** for inline **JS** and **CSS**.

- Then sends the **optimized HTML** to the browser.

### 🧠 Options Explained
| Option | Description |
|--------|-------------|
| **removeComments** | Removes all HTML comments (e.g., `<!-- comment -->`) from the final output to reduce file size. |
| **collapseWhitespace** | Collapses unnecessary spaces, tabs, and line breaks between HTML elements for compact output. |
| **collapseBooleanAttributes** | Simplifies boolean attributes like `checked="true"` → `checked`, `disabled="disabled"` → `disabled`. |
| **removeAttributeQuotes** | Removes quotes around HTML attribute values when they are not required, e.g., `class="chat"` → `class=chat`. |
| **removeRedundantAttributes** | Removes default or redundant HTML attributes like `type="text"` from `<input>`. |
| **removeScriptTypeAttributes** | Removes unnecessary `type="text/javascript"` from `<script>` tags, as it’s implied by default in HTML5. |
| **removeStyleLinkTypeAttributes** | Removes redundant `type="text/css"` from `<link>` and `<style>` tags for cleaner markup. |
| **minifyCSS** | Compresses inline CSS within `<style>` tags and `style` attributes to reduce file size. |
| **minifyJS** | Compresses inline JavaScript within `<script>` tags using Terser for optimized performance. |

---

### 🧾 Example Before & After

**Before:**
```html
<div class="chat-box">
    <script>
        console.log("Chat loaded");
    </script>
</div>
```

**After:**
```html
<div class="chat-box"><script>console.log("Chat loaded");</script></div>
```

---

> Note: Avoid using **express-minify-html-terser** if:

- You serve only API responses (JSON, not HTML).

- You already have a front-end build tool (like **React.js + Vite** or **Next.js**) that handles minification.

### ✅ Benefits

- `Performance:` Smaller, faster-loading pages

- `Automatic:` No need to manually minify HTML

- `Flexible:` Works with any templating engine

- `Production-Ready:` Ideal for deployment optimization
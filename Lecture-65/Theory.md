# 🚀 NODE.JS DEPLOYMENT ON DIGITAL OCCEAN THROUGH TERMINAL

### Step 1: SSH into your server

After creating a droplet, you get your server IP and password. Open your terminal and type:
```bash
ssh root@YOUR_SERVER_IP
```

Replace **YOUR_SERVER_IP** with your droplet’s IP.

Enter the password when prompted. (Note: The terminal will not show characters while typing the password, this is normal.)

### Step 2: Update your server packages

Before installing anything, update the server:

```bash
apt update -y
apt upgrade -y
```

This ensures your server has the latest security updates.

### Step 3: Install Node.js

Install Node.js (LTS version) and npm:
```bash
apt install -y nodejs npm
```

**Check versions:**
```bash
node -v
npm -v
```

### Step 4: Create a project directory

Create a folder for your project:
```bash
mkdir -p /var/www/my-backend
cd /var/www/my-backend
```

**mkdir -p creates the folder, cd navigates inside.**

### Step 5: Clone your GitHub repository

Clone your backend project from GitHub:
```bash
git clone https://USERNAME:TOKEN@github.com/USERNAME/REPO_NAME.git .
```

**Replace**:

**USERNAME** = GitHub username

**TOKEN** = Personal access token (if repo is private)

**REPO_NAME** = Repository name

The "." at the end means “**clone into the current folder**.”

### Step 6: Set environment variables

Create a .env file in the project root:
```bash
nano .env
```

**Paste your environment variables, for example:**
```bash
PORT=8000
WEB_ORIGIN=http://localhost:5173
MONGO_URL=mongodb+srv://youruser:password@cluster.mongodb.net
DB_NAME=my_database
NODE_ENV=production
```

- Save with `CTRL + O` → Enter

- Exit with `CTRL + X`

- Tip: Never push .env to GitHub.

### Step 7: Install project dependencies

Install everything defined in package.json:
```bash
npm install
```

### Step 8: Install PM2

PM2 is a process manager to run your server in the background and auto-restart on crashes:
```bash
npm install -g pm2
```

**Check version:**
```bash
pm2 -v
```

### Step 9: Start your server using PM2

Start the backend:
```bash
pm2 start src/server.js --name my-backend
```

- `--name my-backend` is the process name for PM2.

- Check status:
```bash
pm2 status
```

### Step 10: Enable PM2 to start on reboot

Make your backend start automatically after a server reboot:
```bash
pm2 startup systemd
```

- This command gives you another command in the output. Copy and run it, e.g.:
```bash
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u root --hp /root
```

**Then save current PM2 processes:**
```bash
pm2 save
```

### Step 11: Pull updates from GitHub in future

When you update your code on GitHub:
```bash
cd /var/www/my-backend
git pull origin main   # or master
npm install            # if new dependencies added
pm2 restart my-backend # or simply pm2 restart all to restart all process
```

### Step 12: Test your API

Your backend is live on the IP and port you set:
```bash
http://YOUR_SERVER_IP:8000/api/v1/
```

- Use a browser or Postman to test endpoints.

### Extra Tips

If using a firewall, make sure port 8000 (or your chosen port) is open.

Check logs for errors:
```bash
pm2 logs my-backend
```

# ⚙️ CI/CD PIPELINE WITH GITHUB ACTIONS (AUTOMATE DEPLOYMENT)

## 📘 What is CI/CD?

### ✅ CI — Continuous Integration

Whenever you push code to GitHub:

* Your code automatically gets tested
* Build process runs automatically
* Errors can be detected early

---

### ✅ CD — Continuous Deployment

After successful push:

* GitHub automatically connects to your VPS
* Latest code gets pulled
* Dependencies install/update
* PM2 restarts your backend automatically

This process is called:

# 🚀 Automated Deployment

---

# 🧠 Our Project Setup

We are assuming:

| Service           | Status                             |
| ----------------- | ---------------------------------- |
| VPS               | Hostinger VPS                      |
| Backend           | Already deployed                   |
| Reverse Proxy     | Nginx configured                   |
| Process Manager   | PM2 configured                     |
| Environment Files | `.env.local` and `.env.production` |
| Package           | `cross-env` installed              |

---

# 📂 Example Project Structure

```bash
project/
│
├── .github/
│   └── workflows/
│       └── deployment.yml
│
├── .env.local
├── .env.production
├── package.json
└── index.js
```

---

# 🔐 STEP 1 — Generate SSH Keys

We need SSH keys because:

* GitHub Actions will login into VPS automatically
* Password login is NOT recommended
* SSH keys are secure and automated

---

# 📌 Why Unique SSH Keys Per Project?

Suppose you have:

* ecommerce-api
* chat-app
* booking-system

Each project should have its own SSH key.

Why?

✅ Better security
✅ Easy management
✅ Easy revoke access
✅ Cleaner CI/CD setup

---

# 🖥️ STEP 2 — Open CMD / PowerShell

Open:

```bash
CMD
```

OR

```bash
PowerShell
```

---

# 🔑 STEP 3 — Generate SSH Key Pair

## 📌 Command

```bash
ssh-keygen -t ed25519 -C "ecommerce-api-github-actions" -f ~/.ssh/ecommerce-api
```

---

# 🧠 Command Breakdown

| Part         | Meaning                            |
| ------------ | ---------------------------------- |
| `ssh-keygen` | Generates SSH keys                 |
| `-t ed25519` | Modern secure encryption algorithm |
| `-C`         | Comment/name for identification    |
| `-f`         | File name/location                 |

---

# 📂 Files Created

This command creates:

```bash
~/.ssh/ecommerce-api
```

✅ Private key

AND

```bash
~/.ssh/ecommerce-api.pub
```

✅ Public key

---

# 🔥 IMPORTANT

| File                | Purpose              |
| ------------------- | -------------------- |
| `ecommerce-api`     | PRIVATE KEY (SECRET) |
| `ecommerce-api.pub` | PUBLIC KEY           |

---

# 📌 Windows SSH Folder Location

Usually:

```bash
C:\Users\YOUR_USERNAME\.ssh
```

Example:

```bash
C:\Users\Usman\.ssh
```

---

# 📌 View Your Keys

## Show Public Key

```bash
type C:\Users\Usman\.ssh\ecommerce-api.pub
```

---

## Show Private Key

```bash
type C:\Users\Usman\.ssh\ecommerce-api
```

---

# ⚠️ Copy Both Keys Somewhere Safe

You will need:

| Key         | Used Where          |
| ----------- | ------------------- |
| Private Key | GitHub Secrets      |
| Public Key  | VPS authorized_keys |

---

# 🌐 STEP 4 — SSH Into VPS

## 📌 Command

```bash
ssh root@YOUR_VPS_IP
```

---

# 🧠 Breakdown

| Part          | Meaning             |
| ------------- | ------------------- |
| `ssh`         | Connect through SSH |
| `root`        | VPS username        |
| `@`           | Separator           |
| `YOUR_VPS_IP` | Server IP           |

---

# 📂 STEP 5 — Go To SSH Authorized Keys Location

After login:

```bash
cd ~/.ssh
```

---

# 📌 Why?

This folder stores:

* Authorized SSH public keys
* Known hosts
* SSH configs

---

# 📌 See Existing Files

```bash
ls
```

You may see:

```bash
authorized_keys
```

---

# ✏️ STEP 6 — Open authorized_keys File

```bash
nano authorized_keys
```

---

# 🧠 What is authorized_keys?

This file contains:

# ✅ ALL PUBLIC KEYS ALLOWED TO LOGIN

If a public key exists here:

✅ Server trusts that key

---

# 📋 STEP 7 — Paste Public Key

Paste content of: **ecommerce-api.pub**

Example:

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG7..... ecommerce-api-github-actions
```

---

# 💾 Save File In Nano

Press:

```bash
CTRL + X
```

Then:

```bash
Y
```

Then:

```bash
ENTER
```

---

# 🔒 STEP 8 — Set Correct Permissions

Run:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

# 🧠 Breakdown

| Command                     | Meaning                             |
| --------------------------- | ----------------------------------- |
| `chmod 700 ~/.ssh`          | Only owner can access `.ssh` folder |
| `chmod 600 authorized_keys` | Only owner can read/write file      |

---

# ✅ STEP 9 — Test SSH Key Login

From local machine:

```bash
ssh -i C:\Users\Usman\.ssh\ecommerce-api root@YOUR_VPS_IP
```

---

# 🧠 Breakdown

| Part            | Meaning                      |
| --------------- | ---------------------------- |
| `-i`            | Specify identity/private key |
| `ecommerce-api` | Your private key             |
| `root@IP`       | VPS login                    |

---

# 🚀 STEP 10 — Push Project To GitHub

Your backend project should already exist on GitHub.

Example:

```bash
https://github.com/usmanhameed2017/ecommerce-api
```

---

# ⚙️ STEP 11 — Create GitHub Actions Folder

Inside project:

```bash
mkdir -p .github/workflows
```

---

# 📂 Final Structure

```bash
.github/
└── workflows/
    └── deployment.yml
```

---

# 📝 STEP 12 — Create deployment.yml

## 📌 File

```bash
.github/workflows/deployment.yml
```

---

# 🚀 Full GitHub Actions Workflow

```yaml
name: Deploy Backend To VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:

      - name: Pull Latest Code
        uses: actions/checkout@v4

      - name: Setup SSH
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.VPS_SSH_KEY }}" > ~/.ssh/private_key
          chmod 600 ~/.ssh/private_key

      - name: Connect VPS And Deploy
        run: |
          ssh -o StrictHostKeyChecking=no -i ~/.ssh/private_key root@YOUR_VPS_IP << 'EOF'

            cd /var/www/ecommerce-api

            git pull origin main

            npm install

            pm2 restart ecommerce-api

          EOF
```

---

# 🧠 FULL WORKFLOW EXPLANATION

---

# 🔹 name

```yaml
name: Deploy Backend To VPS
```

Workflow display name.

This appears inside GitHub Actions tab.

---

# 🔹 on

```yaml
on:
  push:
```

Means:

# 🚀 Run workflow when code is pushed

---

# 🔹 branches

```yaml
branches:
  - main
```

Only trigger deployment when:

```bash
git push origin main
```

happens.

---

# 🔹 jobs

```yaml
jobs:
```

Defines tasks/work.

---

# 🔹 deploy

```yaml
deploy:
```

Custom job name.

---

# 🔹 runs-on

```yaml
runs-on: ubuntu-latest
```

GitHub creates temporary Ubuntu server.

Workflow runs there.

---

# 🔹 steps

```yaml
steps:
```

Sequence of tasks.

---

# 🔹 Checkout Code

```yaml
uses: actions/checkout@v4
```

Downloads latest repository code inside GitHub Actions machine.

---

# 🔹 Setup SSH

```yaml
mkdir -p ~/.ssh
```

Creates `.ssh` folder.

---

```yaml
echo "${{ secrets.VPS_SSH_KEY }}" > ~/.ssh/private_key
```

Creates private key file dynamically from GitHub Secrets.

---

```yaml
chmod 600 ~/.ssh/private_key
```

Secure permissions.

---

# 🔹 Connect VPS

```yaml
ssh -i ~/.ssh/private_key root@YOUR_VPS_IP
```

GitHub Actions logs into VPS using SSH key.

---

# 🔹 StrictHostKeyChecking=no

```yaml
-o StrictHostKeyChecking=no
```

Prevents interactive confirmation popup.

Useful for automation.

---

# 🔹 EOF Block

```yaml
<< 'EOF'
```

Everything inside runs ON VPS server.

---

# 🔹 Go To Project Folder

```yaml
cd /var/www/ecommerce-api
```

Move into deployed backend folder.

---

# 🔹 Pull Latest Code

```yaml
git pull origin main
```

Fetch latest changes from GitHub.

---

# 🔹 Install Dependencies

```yaml
npm install
```

Installs new packages if added.

---

# 🔹 Restart PM2

```yaml
pm2 restart ecommerce-api
```

Restarts backend process.

---

# 📌 PM2 Process Name Check

See process names:

```bash
pm2 list
```

Example:

```bash
ecommerce-api
```

Use same exact name.

---

# 🔐 STEP 13 — Add GitHub Secret

Open repository on GitHub.

---

# 📌 Go To

```bash
Repository
→ Settings
→ Secrets and variables
→ Actions
```

---

# 📌 Click

```bash
New repository secret
```

---

# 📌 Secret Name

```bash
VPS_SSH_KEY
```

---

# 📌 Secret Value

Paste PRIVATE key content:

```bash
ecommerce-api
```

NOT `.pub`

---

# ⚠️ IMPORTANT

| Key Type    | GitHub Secret? |
| ----------- | -------------- |
| Private Key | ✅ YES          |
| Public Key  | ❌ NO           |

---

# 🔥 FINAL DEPLOYMENT FLOW

Now whenever you run:

```bash
git push origin main
```

GitHub will automatically:

1. Detect push
2. Start workflow
3. Connect VPS
4. Pull latest code
5. Install packages
6. Restart PM2

---

# 🎉 Fully Automated Deployment Ready

No manual VPS deployment needed anymore.

---

# 📦 BONUS — Environment Setup

Since you already use:

```bash
.env.local
.env.production
```

with `cross-env`

You can use scripts like:

```json
"scripts":{
  "dev":"cross-env NODE_ENV=local nodemon index.js",
  "start":"cross-env NODE_ENV=production node index.js"
}
```

---

# 📌 PM2 Production Start

```bash
pm2 start npm --name "ecommerce-api" -- run start
```

This automatically uses:

```bash
.env.production
```

if your config loads environment based on:

```javascript
process.env.NODE_ENV
```

---

# 🚀 Example Deployment Cycle

```bash
Code Change
↓
git add .
↓
git commit -m "updated api"
↓
git push origin main
↓
GitHub Actions Trigger
↓
SSH Into VPS
↓
git pull
↓
npm install
↓
pm2 restart
↓
Deployment Complete ✅
```
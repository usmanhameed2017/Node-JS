# MONGODB INSTALLATION

### Two Ways Of Installing MongoDB

1. MongoDB Community Edition (Local)
2. MongoDB ATLAS (Cloud Based)

- In `Community Edition`, we need three things:
1. MongoDB Community Edition Installer
2. MongoDB Compass Tool
3. Mongosh (shell)

### Steps

#### Step:01

- Download mongodb community edition installer from the official website of mongodb.

- Visit th website: `https://www.mongodb.com/try/download/community`.

- Click on products > click on Community Edition > Click on Download Community button.

- Scroll down > select platform as `Windows x64` > Click on Download button.

- Now, install it.

- Click on next > Select Complete setup > Click on yes for user account

#### Step:02

- Setup the environment variable with the path.

- Search for environment variable in your system.

- Click on Edit the system `environment variables`.

- Go to C > Program Files > MongoDB > Server > 8.0 > bin.

- Now, copy the complete path.

- Go to environemnt variable window, Click on environment variables button > Double click on path.

- Click on new > paste your path > Click on Ok.

#### Step:03

- Now, install Mongosh.

- Go to google and search for MongoDB shell download. Link: `https://www.mongodb.com/try/download/shell`.

- Scroll down and select package as `msi` and click on download.

- Now, install it.

- Now, check if all these tools has successfully installed in your system.

- Open cmd > type `mongod --version`.

- Now, type command `mongosh` to view mongosh (shell).
# Package.json & Package-lock.json
### Two ways Of Creating `package.json` file.
1. By initialzing project with the command: `npm init`
2. When we install any module or library using npm then `package.json` file is created automatically.

###### Note: we don't create or modify `package-lock.json` file manually because this file is automatically created and managed by the npm.

• The `npm init` command will initialize a project and create the package.json file.

• The `package.json` and `package-lock.json` files are both important components of a Node.js project, but they serve different purposes.

###### `package.json`: This file is primarily used for managing and documenting `metadata` about the project, including its `name`, `version`, `author`, `dependencies`, `scripts`, and other configuration details within json format.

###### `package-lock.json`: This file is generated and updated automatically by npm when installing or updating packages. It is used to lock the exact versions of dependencies installed in the project, ensuring reproducibility and consistent installations across different environments.

### Conclusion
• In summary, the `package.json` file focuses on project metadata and specifying the desired versions of dependencies, while the `package-lock.json` file ensures deterministic installations by locking the exact versions of dependencies and their sub-dependencies.
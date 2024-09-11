# Lab 1: Node.js and npm Fundamentals

## Objective
In this lab, you will learn how to set up Node.js and npm, understand the structure of package.json, create a project using npm, and work with package-lock.json files.

## Prerequisites
- A computer with internet access
- Basic knowledge of command-line operations

## Lab Tasks

### 1. Setting up Node.js and npm

a) Download and install Node.js from the official website: https://nodejs.org/

b) Verify the installation by opening a terminal and running:
```bash
node --version
npm --version
```

### 2. Understanding package.json structure

a) Create a new directory for your project and navigate to it:
```bash
mkdir my-node-project
cd my-node-project
```

b) Initialize a new npm project:
```bash
npm init -y
```

c) Open the generated package.json file in a text editor and examine its structure. It should look similar to this:

```json
{
  "name": "my-node-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

d) Modify the package.json file to add a custom script:

```json
{
  ...
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ...
}
```

### 3. Creating a project using npm

a) Create an index.js file in your project directory:

```javascript
console.log("Hello, Node.js!");
```

b) Install a popular npm package, like lodash:
```bash
npm install lodash
```

c) Modify index.js to use lodash:

```javascript
const _ = require('lodash');

const numbers = [1, 2, 3, 4, 5];
console.log(_.sum(numbers));
```

d) Run your project:
```bash
npm start
```

### 4. Understanding package-lock.json files

a) Examine the generated package-lock.json file. This file locks the versions of your dependencies and their sub-dependencies.

b) Try installing a different version of lodash:
```bash
npm install lodash@4.17.20
```

c) Check how the package-lock.json file has changed.

### 5. Using private npm registries (e.g., Verdaccio) in npm configuration

a) Install Verdaccio globally:
```bash
npm install -g verdaccio
```

b) Start Verdaccio:
```bash
verdaccio
```

c) In a new terminal, add a new user to Verdaccio:
```bash
npm adduser --registry http://localhost:4873
```

d) Modify your project's .npmrc file to use Verdaccio:
```
registry=http://localhost:4873
```

e) Publish your package to Verdaccio:
```bash
npm publish
```

f) Try installing your package from Verdaccio in a new project.

## Conclusion

In this lab, you've learned how to set up Node.js and npm, create and understand the structure of a package.json file, work with npm to create a project and manage dependencies, understand the role of package-lock.json, and use a private npm registry with Verdaccio.

## Additional Exercises

1. Create a more complex Node.js application with multiple dependencies.
2. Experiment with different versions of a package and observe changes in package-lock.json.
3. Set up a project with both dependencies and devDependencies in package.json.

## Resources

- Node.js Documentation: https://nodejs.org/en/docs/
- npm Documentation: https://docs.npmjs.com/
- Verdaccio Documentation: https://verdaccio.org/docs/en/what-is-verdaccio

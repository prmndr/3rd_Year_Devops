# Lab 2: Package Management and Dependencies

## Objective
In this lab, you will learn about advanced package management techniques, working with Artifactory, managing environment variables and secrets, and using npm scopes for organization-level packages.

## Prerequisites
- Completion of Lab 1
- Node.js and npm installed
- Basic understanding of JavaScript

## Lab Tasks

### 1. Artifactory requirements in package.json

a) Set up a free JFrog Artifactory Cloud account: https://jfrog.com/artifactory/start-free/

b) Once your Artifactory instance is ready, navigate to the Artifacts > Packages page and create a new npm repository named "npm-local".

c) In your project's package.json, add the following:

```json
{
  ...
  "publishConfig": {
    "registry": "https://your-instance-name.jfrog.io/artifactory/api/npm/npm-local/"
  }
}
```

### 2. Working with environment variables and secrets management

a) Install the dotenv package:
```bash
npm install dotenv
```

b) Create a .env file in your project root:
```
API_KEY=your_secret_api_key
DATABASE_URL=your_database_url
```

c) Create a config.js file:

```javascript
require('dotenv').config();

module.exports = {
  apiKey: process.env.API_KEY,
  databaseUrl: process.env.DATABASE_URL
};
```

d) Modify your index.js to use these config values:

```javascript
const config = require('./config');

console.log(`API Key: ${config.apiKey}`);
console.log(`Database URL: ${config.databaseUrl}`);
```

e) Add .env to your .gitignore file to prevent committing secrets.

### 3. Deploying npm packages to Artifactory

a) Log in to your Artifactory npm registry:
```bash
npm login --registry=https://your-instance-name.jfrog.io/artifactory/api/npm/npm-local/
```

b) Publish your package:
```bash
npm publish
```

c) Verify that your package appears in the Artifactory UI.

### 4. Using npm scopes for organization-level packages

a) Create a new directory for a scoped package:
```bash
mkdir @myorg-calculator
cd @myorg-calculator
```

b) Initialize a new npm project with a scope:
```bash
npm init --scope=@myorg
```

c) Create an index.js file with a simple calculator function:

```javascript
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = { add, subtract };
```

d) Update the package.json to make the package public:

```json
{
  ...
  "publishConfig": {
    "access": "public"
  }
}
```

e) Publish the scoped package to Artifactory:
```bash
npm publish
```

f) In your main project, install the scoped package:
```bash
npm install @myorg/calculator
```

g) Use the scoped package in your index.js:

```javascript
const calculator = require('@myorg/calculator');

console.log(calculator.add(5, 3));
console.log(calculator.subtract(10, 4));
```

### 5. Managing dependencies with npm

a) Install a production dependency:
```bash
npm install express
```

b) Install a development dependency:
```bash
npm install --save-dev nodemon
```

c) Update your package.json scripts:

```json
{
  ...
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  }
}
```

d) Run your application in development mode:
```bash
npm run dev
```

## Conclusion

In this lab, you've learned how to work with Artifactory, manage environment variables and secrets, deploy packages to Artifactory, use npm scopes for organization-level packages, and manage different types of dependencies.

## Additional Exercises

1. Create multiple scoped packages and use them together in a project.
2. Set up a pre-publish script in your package.json to run tests before publishing.
3. Experiment with different types of dependencies (optionalDependencies, peerDependencies) in your package.json.

## Resources

- npm Documentation on scopes: https://docs.npmjs.com/cli/v7/using-npm/scope
- Artifactory Documentation: https://www.jfrog.com/confluence/display/JFROG/npm+Registry
- dotenv Documentation: https://github.com/motdotla/dotenv#readme

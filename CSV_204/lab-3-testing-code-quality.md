# Lab 3: Testing and Code Quality

## Objective
In this lab, you will learn how to implement unit testing using Jest for JavaScript/TypeScript projects, and how to set up linting and code formatting with ESLint and Prettier.

## Prerequisites
- Completion of Lab 1 and Lab 2
- Node.js and npm installed
- Basic understanding of JavaScript and testing concepts

## Lab Tasks

### 1. Setting up Jest for Unit Testing

a) Install Jest and its types as dev dependencies:
```bash
npm install --save-dev jest @types/jest
```

b) Update your package.json to include a test script:
```json
{
  ...
  "scripts": {
    "test": "jest"
  }
}
```

c) Create a simple function to test. In a new file named `math.js`, add:
```javascript
function sum(a, b) {
  return a + b;
}

module.exports = { sum };
```

d) Create a test file named `math.test.js`:
```javascript
const { sum } = require('./math');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

e) Run the test:
```bash
npm test
```

### 2. Writing More Complex Tests

a) Add more functions to `math.js`:
```javascript
function multiply(a, b) {
  return a * b;
}

function divide(a, b) {
  if (b === 0) throw new Error('Cannot divide by zero');
  return a / b;
}

module.exports = { sum, multiply, divide };
```

b) Add tests for these functions in `math.test.js`:
```javascript
const { sum, multiply, divide } = require('./math');

describe('Math functions', () => {
  test('adds 1 + 2 to equal 3', () => {
    expect(sum(1, 2)).toBe(3);
  });

  test('multiplies 3 * 4 to equal 12', () => {
    expect(multiply(3, 4)).toBe(12);
  });

  test('divides 10 / 2 to equal 5', () => {
    expect(divide(10, 2)).toBe(5);
  });

  test('throws an error when dividing by zero', () => {
    expect(() => divide(5, 0)).toThrow('Cannot divide by zero');
  });
});
```

c) Run the tests again:
```bash
npm test
```

### 3. Setting up ESLint for Linting

a) Install ESLint and its dependencies:
```bash
npm install --save-dev eslint eslint-config-airbnb-base eslint-plugin-import
```

b) Initialize ESLint configuration:
```bash
npx eslint --init
```
Choose options appropriate for your project (e.g., JavaScript modules, browser/Node.js, popular style guide like Airbnb).

c) Add a lint script to your package.json:
```json
{
  ...
  "scripts": {
    "test": "jest",
    "lint": "eslint ."
  }
}
```

d) Run the linter:
```bash
npm run lint
```

e) Fix any linting errors in your code.

### 4. Setting up Prettier for Code Formatting

a) Install Prettier and its ESLint integration:
```bash
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

b) Create a `.prettierrc` file in your project root:
```json
{
  "singleQuote": true,
  "trailingComma": "es5"
}
```

c) Update your ESLint configuration to use Prettier. Add this to your `.eslintrc.js`:
```javascript
module.exports = {
  // ... other configurations
  extends: [
    // ... other extends
    'plugin:prettier/recommended',
  ],
};
```

d) Add a format script to your package.json:
```json
{
  ...
  "scripts": {
    "test": "jest",
    "lint": "eslint .",
    "format": "prettier --write ."
  }
}
```

e) Run the formatter:
```bash
npm run format
```

### 5. Integrating Linting and Formatting in Your Workflow

a) Install husky and lint-staged for pre-commit hooks:
```bash
npm install --save-dev husky lint-staged
```

b) Add the following to your package.json:
```json
{
  ...
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.js": [
      "eslint --fix",
      "prettier --write",
      "jest --findRelatedTests"
    ]
  }
}
```

c) Make a change to your code, commit it, and observe the pre-commit hook in action.

## Conclusion

In this lab, you've learned how to set up and use Jest for unit testing, implement ESLint for code linting, and use Prettier for code formatting. You've also integrated these tools into your development workflow using pre-commit hooks.

## Additional Exercises

1. Write tests for asynchronous functions using Jest.
2. Implement code coverage reporting with Jest.
3. Create custom ESLint rules for your project.
4. Set up continuous integration using GitHub Actions to run tests and linting.

## Resources

- Jest Documentation: https://jestjs.io/docs/getting-started
- ESLint Documentation: https://eslint.org/docs/user-guide/getting-started
- Prettier Documentation: https://prettier.io/docs/en/index.html
- Husky Documentation: https://typicode.github.io/husky/#/

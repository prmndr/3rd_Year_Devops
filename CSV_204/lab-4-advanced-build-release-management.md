# Lab 4: Advanced Build and Release Management

## Objective
In this final lab, you will apply the concepts learned in previous labs to create a more complex project. You'll set up a CI/CD pipeline, work with semantic versioning, and implement advanced npm features.

## Prerequisites
- Completion of Labs 1, 2, and 3
- Node.js and npm installed
- Git installed and configured
- GitHub account

## Lab Tasks

### 1. Setting up a React Application

a) Create a new React application using Create React App:
```bash
npx create-react-app my-react-app
cd my-react-app
```

b) Install additional dependencies:
```bash
npm install axios
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

### 2. Implementing Features and Tests

a) Create a new file `src/api.js`:
```javascript
import axios from 'axios';

export const fetchUser = async (id) => {
  const response = await axios.get(`https://jsonplaceholder.typicode.com/users/${id}`);
  return response.data;
};
```

b) Update `src/App.js`:
```jsx
import React, { useState } from 'react';
import { fetchUser } from './api';

function App() {
  const [userId, setUserId] = useState('');
  const [user, setUser] = useState(null);

  const handleFetch = async () => {
    try {
      const userData = await fetchUser(userId);
      setUser(userData);
    } catch (error) {
      console.error('Error fetching user:', error);
    }
  };

  return (
    <div className="App">
      <input 
        value={userId} 
        onChange={(e) => setUserId(e.target.value)} 
        placeholder="Enter user ID"
      />
      <button onClick={handleFetch}>Fetch User</button>
      {user && (
        <div>
          <h2>{user.name}</h2>
          <p>{user.email}</p>
        </div>
      )}
    </div>
  );
}

export default App;
```

c) Create a test file `src/App.test.js`:
```jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import App from './App';
import { fetchUser } from './api';

jest.mock('./api');

test('fetches and displays user', async () => {
  fetchUser.mockResolvedValueOnce({ id: 1, name: 'John Doe', email: 'john@example.com' });

  render(<App />);

  fireEvent.change(screen.getByPlaceholderText('Enter user ID'), { target: { value: '1' } });
  fireEvent.click(screen.getByText('Fetch User'));

  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
  });
});
```

### 3. Setting up GitHub Actions for CI/CD

a) Create a `.github/workflows/ci.yml` file:
```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14.x'
    - run: npm ci
    - run: npm run build
    - run: npm test
    - run: npm run lint
```

b) Push your changes to GitHub and check the Actions tab to see your workflow running.

### 4. Implementing Semantic Versioning

a) Install semantic-release:
```bash
npm install --save-dev semantic-release @semantic-release/git @semantic-release/github
```

b) Create a `.releaserc` file:
```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}
```

c) Update your GitHub Actions workflow to include semantic-release:
```yaml
# ... previous steps ...
    - name: Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
      run: npx semantic-release
```

### 5. Implementing npm scripts for common tasks

Update your `package.json` with the following scripts:
```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "lint": "eslint .",
    "format": "prettier --write .",
    "prepare": "husky install"
  }
}
```

### 6. Creating a custom npm package

a) Create a new directory for your package:
```bash
mkdir my-utils
cd my-utils
npm init -y
```

b) Create an `index.js` file with some utility functions:
```javascript
const capitalizeString = (str) => str.charAt(0).toUpperCase() + str.slice(1);

const reverseString = (str) => str.split('').reverse().join('');

module.exports = { capitalizeString, reverseString };
```

c) Publish your package to your Artifactory instance:
```bash
npm publish
```

d) Use your custom package in your React app:
```bash
npm install my-utils
```

Update `App.js` to use your utility functions.

### 7. Implementing Feature Flags

a) Install a feature flag library:
```bash
npm install unleash-client
```

b) Set up feature flags in your app:
```javascript
import { initialize, isEnabled } from 'unleash-client';

initialize({
  url: 'https://your-unleash-instance.com/api/',
  appName: 'my-react-app',
  instanceId: 'your-instance-id',
});

// Use feature flags in your components
if (isEnabled('newFeature')) {
  // Render new feature
} else {
  // Render old feature
}
```

## Conclusion

In this final lab, you've applied advanced build and release management concepts to a React application. You've set up a CI/CD pipeline with GitHub Actions, implemented semantic versioning, created custom npm packages, and used feature flags. These skills will be valuable in managing complex software projects and implementing modern development practices.

## Additional Exercises

1. Implement end-to-end tests using Cypress.
2. Set up a staging environment and implement a blue-green deployment strategy.
3. Create a monorepo structure for your main app and utility package.
4. Implement automated dependency updates using Dependabot.

## Resources

- GitHub Actions Documentation: https://docs.github.com/en/actions
- semantic-release Documentation: https://semantic-release.gitbook.io/semantic-release/
- Unleash Feature Flag Documentation: https://docs.getunleash.io/
- Monorepo tools: https://monorepo.tools/

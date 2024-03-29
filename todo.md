# Using Docker Compose with services

## Client configuration

> inspired by a [Dyma.fr](https://dyma.fr/) "[Docker](https://www.docker.com/products/docker-desktop/)" exercice

---

```bash
# Create directories and files
mkdir -p fullstack/{api,client,db,reverse-proxy} && touch fullstack/{docker-compose.dev.yml,docker-compose.prod.yml};
```

```bash
# Navigate to the fullstack directory
cd fullstack;
```

```bash
# Open the fullstack directory in Visual Studio Codium
codium fullstack;
```

```bash
# Navigate to the client directory and create a new React app
cd client;
npx create-react-app client;
```

```bash
# Remove node_modules directory and move all files from client to current directory
rm -rf node_modules/;
mv client/* .;
```

```bash
# Remove the 2nd client directory
rm -rf client/;
```

```bash
# Create a Dockerfile for development
touch Dockerfile.dev;
echo -e "FROM node:alpine\nWORKDIR /app\nCOPY package.json .\nRUN npm install\nCOPY . .\nCMD [ \"npm\", \"start\" ]" >> Dockerfile.dev;
```

```bash
# Go back to the fullstack directory
cd ..;
```

```bash
# Create a docker-compose.dev.yml file for development
echo -e "version: '3.9'\nservices:\n client:\n  stdin_open: true\n  tty: true\n  ports:\n    - '3000:3000'\n  build:\n    dockerfile: Dockerfile.dev\n    context: ./client\n  volumes:\n    - type: bind\n      source: ./client\n      target: /home/node\n    - type: volume\n      target: /home/node/node_modules" > docker-compose.dev.yml;
```

---

#### Download and install Docker Desktop

- Visit [Docker website](https://www.docker.com/products/docker-desktop/), & download "**Docker.dmg**",

- install it in the "/Applications" directory.

- then, start Docker.

```bash
# Verify Docker version
docker-compose --version; #Docker version 24.0.7, build afdd53b4e3
```

```bash
# Install Docker Compose using Homebrew
brew install docker-compose;
```

```bash
# Create a symbolic link for Docker Compose
mkdir -p ~/.docker/cli-plugins;
ln -sfn $(brew --prefix)/opt/docker-compose/bin/docker-compose ~/.docker/cli-plugins/docker-compose;
```

```bash
# Reload the shell
reload;
```

```bash
# Verify Docker Compose version
docker-compose version; #Docker Compose version 2.24.0
```

---

#### Start the services defined in the "**docker-compose.dev.yml**" file and open the React page in browser

```bash
docker compose -f docker-compose.dev.yml up --build;
#../.. webpack compiled successfully
```

```bash
open http://localhost:3000/;
```

---

#### "**Git**" : initialize, configure & add and commit your changes

```bash
# Initialize a Git repository
git init;
```

```bash
# Add a .gitignore file
echo -e "# Ignore node_modules in all directories\nnode_modules/\n# Ignore all log files\n*.log\n# Ignore all .DS_Store files (Mac OS specific)\n.DS_Store\n# Ignore all .env files (contains sensitive data)\n.env\n# Ignore all .cache folders (generated by bundlers)\n.cache/\n# Ignore all dist folders (output of a build process)\ndist/\n# Ignore all coverage folders (generated by testing)\ncoverage/\n# Ignore all .vscode folders (created by Visual Studio Code IDE)\n.vscode/" > .gitignore;
```

```bash
# Check the status of the repository
git status;

# Add all the changes in the repository to the staging area
git add .;

# Commit the changes with a message
git commit -m "Initial commit";
```

```bash
# initialize your github distant repository
git branch -M main;
git remote add origin git@github.com:yourgithubrepo/Fullstack-docker-compose-test-with-services.git;
git push -u origin main;
```

---

#### Modify the application source code

For testing the API, modify the source code of React app.

Replace the content of "**client/src/App.js**" file with the following code:

```js
import logo from './logo.svg';
import './App.css';
import { useEffect, useState } from 'react';

function App() {
  const [count, setCount] = useState();
  useEffect(() => {
    async function fetchCount() {
      try {
        const response = await fetch('/api/count');
        if (response.ok) {
          setCount(await response.json());
        }
      } catch (error) {
        console.log(error);
      }
    }
    fetchCount();
  }, []);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <p>Count: { count }</p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
```

---

#### next step to do : configure the "**reverse-proxy**"

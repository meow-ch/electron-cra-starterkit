React-TypeScript-Electron sample with Create React App and Electron Builder
===========================================================================

[Source, yhirose](https://github.com/yhirose/react-typescript-electron-sample-with-create-react-app-and-electron-builder/blob/master/package.json)

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app) with `--template typescript`option.

On the top of it, the following features have been added with relatively small changes:

* TypeScript supports for Electron main process source code
* Hot-reload support for Electron app
* Electron Builder support

## Available Scripts in addition to the existing ones

### `npm run electron:dev`

Runs the Electron app in the development mode.

The Electron app will reload if you make edits in the `electron` directory.<br>
You will also see any lint errors in the console.

### `npm run electron:build`

Builds the Electron app package for production to the `dist` folder.

Your Electron app is ready to be distributed!

## Project directory structure

```bash
my-app/
├── package.json
│
## render process
├── tsconfig.json
├── public/
├── src/
│
## main process
├── electron/
│   ├── main.ts
│   └── tsconfig.json
│
## build output
├── build/
│   ├── index.html
│   ├── static/
│   │   ├── css/
│   │   └── js/
│   │
│   └── electron/
│      └── main.js
│
## distribution packages
└── dist/
    ├── mac/
    │   └── my-app.app
    └── my-app-0.1.0.dmg
```

## Do it yourself from scratch

### Generate a React project and install npm dependencies

```bash
npx create-react-app my-app --template typescript
cd my-app
yarn add @types/electron-devtools-installer electron-devtools-installer electron-is-dev electron-reload
yarn add -D concurrently electron electron-builder wait-on cross-env
```

### Make Electron main process source file

#### electron/tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "sourceMap": true,
    "strict": true,
    "outDir": "../build", // Output transpiled files to build/electron/
    "rootDir": "../",
    "noEmitOnError": true,
    "typeRoots": [
      "node_modules/@types"
    ]
  }
}
```

#### electron/main.ts

```ts
import { app, BrowserWindow } from 'electron';
import * as path from 'path';
import * as isDev from 'electron-is-dev';
import installExtension, { REACT_DEVELOPER_TOOLS } from "electron-devtools-installer";

let win: BrowserWindow | null = null;

function createWindow() {
  win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  if (isDev) {
    win.loadURL('http://localhost:3000/index.html');
  } else {
    // 'build/index.html'
    win.loadURL(`file://${__dirname}/../index.html`);
  }

  win.on('closed', () => win = null);

  // Hot Reloading
  if (isDev) {
    // 'node_modules/.bin/electronPath'
    require('electron-reload')(__dirname, {
      electron: path.join(__dirname, '..', '..', 'node_modules', '.bin', 'electron'),
      forceHardReset: true,
      hardResetMethod: 'exit'
    });
  }

  // DevTools
  installExtension(REACT_DEVELOPER_TOOLS)
    .then((name) => console.log(`Added Extension:  ${name}`))
    .catch((err) => console.log('An error occurred: ', err));

  if (isDev) {
    win.webContents.openDevTools();
  }
}

app.on('ready', createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  if (win === null) {
    createWindow();
  }
});
```

### Adjust package.json

#### Add properties for Electron

```json
  "homepage": ".", # see https://create-react-app.dev/docs/deployment#serving-the-same-build-from-different-paths
  "main": "build/electron/main.js",
```

#### Add properties for Electron Builder

```json
  "author": "Your Name",
  "description": "React-TypeScript-Electron sample with Create React App and Electron Builder",
  ...
  "build": {
    "extends": null, # see https://github.com/electron-userland/electron-builder/issues/2030#issuecomment-386720420
    "files": [
      "build/**/*"
    ],
    "directories": {
      "buildResources": "assets" # change the resource directory from 'build' to 'assets'
    }
  },
```

#### Add scripts

```json
  "scripts": {
    "postinstall": "electron-builder install-app-deps",
    "electron:dev": "concurrently \"cross-env BROWSER=none yarn start\" \"wait-on http://127.0.0.1:3000 && tsc -p electron -w\" \"wait-on http://127.0.0.1:3000 && tsc -p electron && electron .\"",
    "electron:build": "yarn build && tsc -p electron && electron-builder",
```

## Many thanks to the following articles!

- [⚡️ From React to an Electron app ready for production](https://medium.com/@kitze/%EF%B8%8F-from-react-to-an-electron-app-ready-for-production-a0468ecb1da3)
- [How to build an Electron app using Create React App and Electron Builder](https://www.codementor.io/randyfindley/how-to-build-an-electron-app-using-create-react-app-and-electron-builder-ss1k0sfer)
- [Application entry file reset to default (react-cra detected and config changed incorrectly)](https://github.com/electron-userland/electron-builder/issues/2030)
- [Serving the Same Build from Different Paths](https://create-react-app.dev/docs/deployment#serving-the-same-build-from-different-paths)

## Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

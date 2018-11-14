## NPM project and git
- package.json - *add to git*
- package-lock.json - *add to git*
- node_modules (folder) - *exclude from git*

To recreate `node_modules` call `npm install`
## Adding webpack
### Instal dependencies
```
npm install --save-dev webpack
npm install --save-dev webpack-cli
```
### Project structure
```
|- package.json
|- webpack.config.js
|- /dist
  |- main.js
  |- index.html
|- /src
  |- index.js
|- /node_modules
```
`dist/main.js` is a bundle file created by webpack. Shouldn't be in git repository.

`dist/index.html` is a base web page including bundle file
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>App template</title>
</head>
<body>
    <script src="main.js"></script>
</body>
</html>
```
`src/index.js` is main javascript file based on which webpack builds `main.js`. Files added to bundle will come from imports starting here.
```javascript
function component() {
    let element = document.createElement('div');
    element.innerHTML = "Hello web pack";

    return element;
}

document.body.appendChild(component());
```
`webpack.config.js` additional configuration file
```javascript
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist')
    }
};

```
In `package.json` remove `main` entry, make package private and add build script
```javascript
{
  "name": "web-app-template",
  "version": "1.0.0",
  "description": "Template project for web apps",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.25.1",
    "webpack-cli": "^3.1.2"
  }
}
```
### Build project
`npm run build`
## React
Sources:
- [How to Create a React app from scratch using Webpack 4](https://medium.freecodecamp.org/part-1-react-app-from-scratch-using-webpack-4-562b1d231e75)
- [Tutorial: How to set up React, webpack 4, and Babel 7 (2018)](https://www.valentinog.com/blog/react-webpack-babel/)

### Improve webpack with modes (this will remove worrning)
Modify script section in package.js
```javascript
    "start": "webpack --mode development",
    "build": "webpack --mode production"
```
### Install react
`npm install --save react react-dom`
Add dev dependencies
`npm install --save-dev @babel/core babel-loader @babel/preset-env @babel/preset-react`

- babel-core - ES6 -> ES5 transformation
- babel-loader - webpack helper to transfer dependencies using babel instead webpack (ES6 imports support)
- babel-preset-env - choose polyfills based on browsers we want to support
- babel-preset-react - babel react plugin to support JSX

Add `.bablerc` to provide babel configuration
```javascript
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```
Move `index.html` from `dist` to `src` and add plugin to generate new one in dist.
`npm install --save-dev  html-webpack-plugin`
### Automate build process with webpack server
Install server plugin `npm install --save-dev webpack-dev-server`

Modify start script
```javascript
"start": "webpack-dev-server --mode development --open",
```
This will start nodjs server listaning on localhost:8080 and open browser. It will also rebuild and refresh page after changes.

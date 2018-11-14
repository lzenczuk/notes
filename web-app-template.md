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

# ExpressJS + React - Basic Setup

# Setting up React with Express

### Basic Node Server with Express

Create the folder

``` bash
$ mkdir my-app
```

create your `package.json` file using [npm init](https://docs.npmjs.com/cli/init)
``` bash
$ cd my-app
$ npm init --yes
```

Install [Express](https://expressjs.com/)

``` bash
npm install --save express
```

Create a `server.js` file in the `my-app` folder and add the following code to start a basic node server

``` javascript
var express = require('express');

var app = express();

app.listen(3000, function () {
    console.log('Example app listening on port 3000!');
});
```

Try running

``` bash
$ node server.js
```

If all is well you should see the following on the console

```bash
Example app listening on port 3000!
```

###babel-loader

[babel-loader](https://github.com/babel/babel-loader) allows transpiling JavaScript files using Babel and webpack

```bash
$ npm install babel-plugin-transform-class-properties babel-plugin-transform-runtime \
            babel-polyfill babel-preset-es2015 babel-preset-react babel-preset-stage-0 \
            babel-register --save
```

> 
- **babel-register** allows you to use babel in nodejs
- **babel-polyfill** emulates full ES6 environment (includes core.js)
- **babel-preset-es2015**, **babel-preset-stage-0**, **babel-preset-react** are presets to enable most of the ES6 features as well as some needed for react, and jsx.
- **babel-plugin-transform-runtime** allows using async/await operators
- **babel-plugin-transform-class-properties** is for using static variables inside ES6 classes 

create a new file `.babelrc` and add the following code to it

```json
{
  "presets": [
    "es2015",
    "stage-0",
    "react"
  ],
  "plugins": [
    "transform-runtime",
    "transform-class-properties"
  ]
}
```

add the following line of code to the top of `server.js`

```
require('babel-register');
```

In `server.js` add the following line after `var app = express();`
```
app.use('/', express.static('public'));
```

> [express.static(<dirname>)](https://expressjs.com/en/starter/static-files.html) is a ExpressJS middleware that allows us to serve up static files from the directory 
> Express looks up the files relative to the static directory, so the name of the static directory is not part of the URL.

We will create the `public` folder in the subsequent steps

###index.html

Create the `public` folder inside the `my-app` folder and create a file `public/index.html`

Add the following to `index.html`

```vbscript-html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hello, world</title>
</head>
<body>
  <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
</body>
</html>
```

###Build the bundle.js

Install webpack and babel-loader to package the babel javascript files.

```bash
npm install webpack babel-loader react react-dom --save
```


###React

Create a folder `src` in `my-app` folder and create our React client file in `src/client.js`
Add the following code to `src/client.js`

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```
 
 It does nothing but renders Hello, world! inside h1 element to container, which is an element with id=”root”.
We need to create that element in index.html. Simply add `<div id=”root”></div>`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hello, world</title>
</head>
<body>
  <div id="root"></div>
  <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
</body>
</html>
```

Now, to build bundle.js, let’s write a config file for webpack bundler

create a file `webpack.config.js` in the project root folder and add the following content

```javascript
module.exports = {
  entry: './src/client.js',
  output: {
    path: './public',
    filename: 'bundle.js'       
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.json'] 
  }
};
```

Before we run, lets install the npm package [concurrently](https://www.npmjs.com/package/concurrently) which allows us to run multiple npm commands concurrently

```
$ npm install concurrently --save-dev
```

extend the `scripts` section of your `package.json` to the following

```json
"scripts": {
    "webpack-watch": "webpack -w",
    "express-server": "node ./server",
    "dev": "concurrently --kill-others \"npm run webpack-watch\" \"npm run express-server\"",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
```

`webpack -w` 

> When using watch mode, webpack installs file watchers to all files, which were used in the compilation process. If any change is detected, it’ll run the compilation again. When caching is enabled, webpack keeps each module in memory and will reuse it if it isn’t changed.

`node <server>`

> command to run the node server

`concurrently --kill-others \"npm run webpack-watch\" \"npm run express-server\"`

> Run the node server with auto reload on change (using webpack -w)
> The `--kill-others` switch kills all commands if one dies

The "keys" in the scripts are aliases for running each of the commands in terminal. To start your server, run:

```bash
$ npm run dev
```

###Hot reloading on server changes

`webpack -w` watches for changes in the compiled files, which is mostly your client/view code.

To enable hot reloading on server code changes, we will use [nodemon](https://github.com/remy/nodemon)

install nodemon
```
$ npm install --save-dev nodemon
```

in package.json, under `"scripts"` change `"express-server": "node ./server"` 
to `"express-server": "nodemon ./server"`

```json
"scripts": {
    "webpack-watch": "webpack -w",
    "express-server": "nodemon ./server",
    "dev": "concurrently --kill-others \"npm run webpack-watch\" \"npm run express-server\"",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
```

**Major Source:** https://medium.com/@viatsko/react-for-beginners-part-1-setting-up-repository-babel-express-web-server-webpack-a3a90cc05d1e#.xoms2grhy

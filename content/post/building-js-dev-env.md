+++
categories = ["javascript"]
description = "How to setup a JS development environment in 2018"
featured = ""
featuredpath = "/img"
title = "Building a Javascript Development Environment"
date = 2018-06-04T14:44:51+03:00
+++

This blog post is a summary of the excellent [Pluralsight Course](https://app.pluralsight.com/library/courses/javascript-development-environment/table-of-contents) by [Cory House](https://twitter.com/housecor).

# Editor and Configuration

First of all, editor of choice here is surprise surprise [VS Code](https://code.visualstudio.com/). I'm actually happly surprised that [Erich Gamma](https://github.com/egamma) is behind this.

Use [EditorConfig](https://editorconfig.org/) to manage, well, editor configurations. Tabs VS spaces, etc. Note that VS Code need to install a plugin for it to work.

The example `.editorconfig` file:

{{< highlight javascript>}}

root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
charset = utf-8

[*.md]
trim_trailing_whitespace = true

{{< /highlight >}}


# Package Management

## Package Manager

The choice of our package manager is [npm](https://www.npmjs.com/).

The `package.json` has all the packages that will be used in the course.

{{< highlight javascript>}}


{
  "name": "javascript-development-environment",
  "version": "1.0.0",
  "description": "JavaScript development environment Pluralsight course by Cory House",
  "scripts": {},
  "author": "Cory House",
  "license": "MIT",
  "dependencies": {
    "npm": "^6.0.1",
    "whatwg-fetch": "1.0.0"
  },
  "devDependencies": {
    "babel-cli": "6.16.0",
    "babel-core": "6.17.0",
    "babel-loader": "6.2.5",
    "babel-preset-latest": "6.16.0",
    "babel-register": "6.16.3",
    "chai": "3.5.0",
    "chalk": "1.1.3",
    "cheerio": "0.22.0",
    "compression": "1.6.2",
    "cross-env": "3.1.3",
    "css-loader": "0.25.0",
    "eslint": "3.8.1",
    "eslint-plugin-import": "2.0.1",
    "eslint-watch": "2.1.14",
    "express": "4.14.0",
    "extract-text-webpack-plugin": "1.0.1",
    "html-webpack-plugin": "2.22.0",
    "jsdom": "9.8.0",
    "json-schema-faker": "0.3.6",
    "json-server": "0.8.22",
    "localtunnel": "1.8.1",
    "mocha": "3.1.2",
    "nock": "8.1.0",
    "npm-run-all": "3.1.1",
    "nsp": "2.6.2",
    "numeral": "1.5.3",
    "open": "0.0.5",
    "rimraf": "2.5.4",
    "style-loader": "0.13.1",
    "webpack": "1.13.2",
    "webpack-dev-middleware": "1.8.4",
    "webpack-hot-middleware": "2.13.0",
    "webpack-md5-hash": "0.0.5"
  }
}

{{< /highlight >}}

To install the packages, do `npm install`.

## Package Security

Since everyone can submit to npm, it is better to check for any vulnerabilities before using packages.

https://nodesecurity.io/

To install:

`npm install -g nsp`

To run:

`nsp check`

# Development Web Server

We are gonna use [Express](https://expressjs.com/) as our development web server.

Create the `/buildScript/srcServer.js`:

{{< highlight javascript>}}

var express = require('express');
var path = require('path');
var open = require('open');

var port = 3000;
var app = express();

app.get('/', function(req, res) {
  res.sendFile(path.join(__dirname, '../src/index.html'));
});

app.listen(port, function(err) {
  if (err) {
    console.log(err);
  } else{
    open('http://localhost:' + port);
  }
});

{{< /highlight >}}

Then create the `/src/index.html`:

{{< highlight html>}}

<html>
  <body>
    <h1>Hello World!!</h1>
  </body>
</html>

{{< /highlight >}}


# Sharing Work In Progress

For expose your WIP to the world, let's use [localtunnel](https://localtunnel.github.io/www/).

`npm install -g localtunnel`

Start your app.

`lt --port 8000`

# Automation

We choose [npm script](https://docs.npmjs.com/misc/scripts) here. Let's try it out.

## npm scripts

First, write the script to start the dev server, in `package.json`:

{{< highlight javascript>}}

...

"scripts": {
    "start": "node buildScript/srcServer.js"
  },
  
...  

{{< /highlight >}}

Now we can start the server by just `npm start`

## Pre/Post Hooks

Create `buildScript/startMessage.js`:

{{< highlight javascript>}}

var chalk = require('chalk');
console.log(chalk.green('Starting app in dev mode...'));

{{< /highlight >}}

Then add `prestart` to the scripts:

{{< highlight javascript>}}

...

"scripts": {
    "prestart": "node buildScript/startMessage.js", 
    "start": "node buildScript/srcServer.js"
  },
  
...  

{{< /highlight >}}

Now if we run `npm start`, the prestart script will be ran before the start script.

## Add Security Check and Share

We now create a script to run security check and share:

{{< highlight javascript>}}

...

"scripts": {
    "prestart": "node buildScript/startMessage.js", 
    "start": "node buildScript/srcServer.js",
    "security-check": "nsp check;exit 0",
    "share": "lt --port 3000"
  },
  
...  

{{< /highlight >}}

Notice that in this way, `nsp` doesn't need to be installed globally.

One thing that is not mentioned in the video is that if `nsp check` finds some vulnerabilities, it will fail npm. To fix this, we can just force it to always return 0.

## Concurrent Tasks

Run mutiple tasks in parallel:

{{< highlight javascript>}}

...
"start": "npm-run-all --parallel security-check open:src",
"open:src": "node buildScripts/srcServer.js",
...
{{< /highlight >}}


# Transpiling

We choose [Babel](https://babeljs.io/) to *compile* newer javascript code to older one.

Let's first add the `.babelrc` to the root of our project:

{{< highlight javascript>}}

{
  "presets": [
    "latest"
  ]
}

{{< /highlight >}}

This basically says use the latest JS.

Let's try to use the module feature from ES6 in `startMessage.js`:

{{< highlight javascript>}}

import chalk from 'chalk';

{{< /highlight >}}

Now in `package.json` we use `babel-node` instead of ´node´.

{{< highlight javascript>}}
...
  "prestart": "babel-node buildScripts/startMessage.js",
...
{{< /highlight >}}

# Bundling

We choose ES6 Modules to be the module format and [webpack](https://webpack.js.org/) as the bundler.

## Configure Webpack with Express

Now create a `webpack.config.dev.js` in the root:

{{< highlight javascript>}}

import path from 'path';

export default {
  debug: true,
  devtool: 'inline-source-map',
  noInfo: false,
  entry: [
    path.resolve(__dirname, 'src/index')
  ],
  target: 'web',
  output: {
    path: path.resolve(__dirname, 'src'),
    publicPath: '/',
    filename: 'bundle.js'
  },
  plugins: [],
  module: {
    loaders: [
      {test: /\.js$/, exclude: /node_modules/, loaders: ['babel']},
      {test: /\.css$/, loaders: ['style','css']}
    ]
  }
}

{{< /highlight >}}

Then we need to configure Express to use it.

In `/buildScripts/srcServer.js`:

{{< highlight javascript>}}

...

import webpack from 'webpack'
import config from '../webpack.config.dev'

const port = 3000
const app = express()
const compiler = webpack(config)

app.use(require('webpack-dev-middleware')(compiler, {
  noInfo: true,
  publicPath: config.output.publicPath
}))

...

{{< /highlight >}}


## Create an App Entry Point

First create `/src/index.js`:

{{< highlight javascript>}}

import numeral from 'numeral'

const courseValue = numeral(1000).format('$0,0.00')
console.log(`I would pay ${courseValue} for this course!`)

{{< /highlight >}}

Now start the app, you should see that `bundle.js` from the browser, it is not really a physical file but in memory.

## Handling CSS with Webpack

First let's create `/src/index.css`:

{{< highlight css>}}
body {
  font-family: sans-serif;
}

table th {
  padding: 5px
}
{{< /highlight >}}


Now we can *import* this css into our `index.html` through `index.js`.

In `index.js` add one line:

{{< highlight javascript>}}
import './index.js'
{{< /highlight >}}

# Sourcemap

Sourcemap allows us to see the original code in the browser console. We have already configured it in our webpack config

`devtool: 'inline-source-map'`.

Now if you try to set a `debugger` break point in the `index.js`, you can see the original code in the browser console.

The cool thing is that this sourcemap will only be downloaded when the console is open, neat.

# Linting

We choose [ESLint](https://eslint.org/).

Create `/.eslintrc.json`:

{{< highlight javascript>}}
{
  "root": true,
  "extends": [
    "eslint:recommended",
    "plugin:import/errors",
    "plugin:import/warnings"
  ],
  "parserOptions": {
    "ecmaVersion": 7,
    "sourceType": "module"
  },
  "env": {
    "browser": true,
    "node": true,
    "mocha": true
  },
  "rules": {
    "no-console": 1
  }
}
{{< /highlight >}}

Next, create a *lint* script in the `package.json`:
{{< highlight javascript>}}
...
"scripts": {
    "lint": "esw webpack.config.* src buildScripts --color; exit 0"
  },
...
{{< /highlight >}}

## Watch files

To watch for file changes and run eslint, we create another script, and add it to the `start` script.

{{< highlight javascript>}}
...
"scripts": {
     ...
     "start": "npm-run-all --parallel security-check open:src lint:watch",
     "lint:watch": "npm run lint -- --watch",
     ...
  },
...
{{< /highlight >}}


# Testing and Continuous Integration

First of all, let's frame the scope here, we are talking about unit testing, not UI testing.

OK, now we have to make 6 decisions:

1. Testing Framework: [Mocha](https://mochajs.org/)
2. Assertion Library: [Chai](http://www.chaijs.com/)
3. Helper Library: [JSDOM](https://github.com/jsdom/jsdom), [Cheerio](https://cheerio.js.org/)
4. Where to Run Tests: Here we choose JSDOM again.
5. Where do Test Files Belong? Alongside the file being tested.
6. When should tests run? Unit Tests Should Run When You Hit Save.

Now we have made the decisions, let's it all setup.

First let's add a script to configure the tests `/buildScripts/testSetup.js`:


{{< highlight javascript>}}
// This file is't transpiled, so must use CommonJS and ES5

// Register babel to transpile before our tests run.
require('babel-register')();

// Disable webpack features that Mocha doesn't understand
require.extensions['.css'] = function() {};

{{< /highlight >}}

Now create a task to run tests:


{{< highlight javascript>}}
"scripts": {
     ...
     "test": "mocha --reporter progress buildScripts/testSetup.js \"src/**/*.test.js\"",
     ...
  },
...
{{< /highlight >}}


Now let's write some simple tests to try it out.

Create `/src/index.test.js`:

{{< highlight javascript>}}

import {expect} from 'chai';
import jsdom from 'jsdom';
import fs from 'fs';

describe('Our first test', () => {
  it('should pass', () => {
    expect(true).to.equal(true);
  });
});

describe('index.html', () => {
  it('should say hello', (done) => {
    const index = fs.readFileSync("./src/index.html", "utf-8");
    jsdom.env(index, function(err, window) {
      const h1 = window.document.getElementsByTagName('h1')[0];
      expect(h1.innerHTML).to.equal("Hello World!");
      done();
      window.close();
    });
  })
})

{{< /highlight >}}

Now if you run `npm test`, you will see 2 tests pass.

OK, let's next configure the tests to be ran on every save.

Add the following task in `package.json` and add it in `start`:


{{< highlight javascript>}}
"scripts": {
     ...
     "start": "npm-run-all --parallel security-check open:src lint:watch test:watch",
     "test:watch": "npm run test -- --watch"
     ...
  },
{{< /highlight >}}

## Setup Travis CI

Go to [Travis](https://travis-ci.org) and login with github account, go to profile and enable the js-dev-env project.

Now create `/.travis.yml`:

{{< highlight yaml>}}

language: node_js
node_js:
  - "6"

{{< /highlight >}}

That's all you need to setup Travis, now just wait and see the result.



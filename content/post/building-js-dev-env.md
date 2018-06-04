+++
categories = ["javascript"]
description = "How to setup a JS development environment in 2018"
featured = ""
featuredpath = "/img"
title = "Building a Javascript Development Environment"
date = 2018-06-04T14:44:51+03:00
draft = true
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
    "security-check": "nsp check",
    "share": "lt --port 3000"
  },
  
...  

{{< /highlight >}}

Notice that in this way, `nsp` doesn't need to be installed globally.

## Concurrent Tasks

Run mutiple tasks in parallel:
{{< highlight javascript>}}
...
"start": "npm-run-all --parallel security-check open:src",
"open:src": "node buildScripts/srcServer.js",
...
{{< /highlight >}}

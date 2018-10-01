+++
author = "Guowei Lv"
categories = ["React"]
keywords = ["ReactJS", "fullstack react", "react tutorial"]
description = "Yet another React tutorial"
featured = "fullstackreact.jpg"
featuredpath = "/img"
title = "FullStack React Distilled 1"
date = 2018-10-01T19:18:14+03:00
+++

I decided to learn the React ecosystem by reading the book [FullStack React](https://www.fullstackreact.com/). It claims itself the single resource you need to learn ReactJS and the surronding ecosystem. Since the book is quite thick, I think it is better to distill it into several blog posts.

The first example project is a voting app.

# Step 0 - Basic Setup

First of all, I will be using VS Code, it is simpley awesome.

There is a bit of a hassle in setting up the VS Code to work nicely with React project. But I already figured out [how to do it](https://www.lvguowei.me/post/vscode-eslint-prettier/), so you have less head banging.

The book starts with very basic React setup by putting a bunch of html and css and js files together. I think we can do better than that by directly using [Create React App](https://reactjs.org/docs/create-a-new-react-app.html#create-react-app).

So basically all you need to do is

{{< highlight sh >}}
npx create-react-app my-app
cd my-app
npm start
{{< /highlight >}}



# Your First React Component

Fisrt, modify the `index.html` file to be the following:

{{< highlight html >}}

<!DOCTYPE html>
<html lang="en">

<head>
  <link rel="stylesheet" href="./semantic-dist/semantic.css" />
  <title>Voting App</title>
</head>

<body>
  <div class="main ui text container">
    <h1 class="ui dividing centered header">Popular Products</h1>
    <div id="root"></div>
  </div>
</body>

</html>
{{< /highlight >}}

It is not hard to guess that React is all about javascript. There is almost no static content in the html file, except for a placeholder `<div id="root">`, where React will put all the generated content.

Now we can create our first React component: a `ProductList`.

{{< highlight js >}}

import React, { Component } from "react";
import "./App.css";

class ProductList extends Component {
  render() {
    return (
      <div className="ui unstackable items">
        Hello, friend! I am a basic React component.
      </div>
    );
  }
}

export default ProductList;

{{< /highlight >}}

This is pretty dummy, but don't worry. Let's try to make it shown on the screen first.

Modify the `index.js` to be the following:

{{< highlight js >}}

import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import ProductList from "./ProductList";
import registerServiceWorker from "./registerServiceWorker";

ReactDOM.render(<ProductList />, document.getElementById("root"));
registerServiceWorker();

{{< /highlight >}}

This basically says render the `ProductList` component inside the `root div`.

That's it! Now you should see this.

{{< figure src="/img/fullstackreact-screenshot-1.png" >}}

[Source Code](https://github.com/lvguowei/fullstackreact/commit/04f664e2028f5333cdd331799f9e81f740ec5209)

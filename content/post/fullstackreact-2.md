+++
author = "Guowei Lv"
categories = ["React"]
keywords = ["ReactJS", "fullstack react", "react tutorial"]
description = "Yet another React tutorial"
featured = "fullstackreact.jpg"
featuredpath = "/img"
title = "FullStack React Distilled 2"
date = 2018-10-05T19:09:09+03:00
+++

# Component properties

>Component takes **properties**, and spits out views on screen.

Let's create the `Product` component to show how to use properties.

{{< highlight js >}}

import React from "react";

class Product extends React.Component {
  render() {
    return (
      <div className="item">
        <div className="image">
          <img src={this.props.productImageUrl} alt="" />
        </div>
        <div className="middle aligned content">
          <div className="header">
            <a>
              <i className="large caret up icon" />
            </a>
            {this.props.votes}
          </div>
          <div className="description">
            <a href={this.props.url}>{this.props.title}</a>
            <p>{this.props.description}</p>
          </div>
          <div className="extra">
            <span>Submitted by:</span>
            <img
              className="ui avatar image"
              src={this.props.submitterAvatarUrl}
              alt=""
            />
          </div>
        </div>
      </div>
    );
  }
}

export default Product;

{{< /highlight >}}

Notice how we use `this.props` all over the place. These properties are passed in when we create the component:

{{< highlight js >}}

import React, { Component } from "react";
import "./App.css";
import Product from "./Product";
import products from "./Seed";

class ProductList extends Component {
  render() {
    const product = products[0];
    return (
      <div className="ui unstackable items">
        <Product
          id={product.id}
          title={product.title}
          description={product.description}
          url={product.url}
          votes={product.votes}
          submitterAvatarUrl={product.submitterAvatarUrl}
          productImageUrl={product.productImageUrl}
        />
      </div>
    );
  }
}

export default ProductList;

{{< /highlight >}}

Now the list has one item which looks like

{{< figure src="/img/fullstackreact-screenshot-2.png" >}}

By using properties, components can be easily reused.

[Source Code](https://github.com/lvguowei/fullstackreact/commit/178cb8c5498b1931a1619f25ec978bd29311644a)

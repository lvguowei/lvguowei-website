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

# A List of Products

Next, let's show a list of products instead of one.

It is very simple to do in React.

First, we sort the products based on the number of votes.

Second, we map the products to a list of `Product` components.

Last, return the list of `Product` components in the render function.

{{< highlight js >}}

class ProductList extends Component {
  render() {
    const sorted = products.sort((a, b) => b.votes - a.votes);
    const productComponents = sorted.map(product => (
      <Product
        key={"product-" + product.id}
        id={product.id}
        title={product.title}
        description={product.description}
        url={product.url}
        votes={product.votes}
        submitterAvatarUrl={product.submitterAvatarUrl}
        productImageUrl={product.productImageUrl}
      />
    ));

    return <div className="ui unstackable items">{productComponents}</div>;
  }
}

{{< /highlight >}}

{{< figure src="/img/fullstackreact-screenshot-3.png" >}}


[Source code](https://github.com/lvguowei/fullstackreact/tree/3861b008fa4878c0da877ca5bfbc055ede34d583)

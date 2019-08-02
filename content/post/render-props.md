+++
author = "Guowei"
categories = ["JavaScript"]
keywords = ["React", "render props", "frontend", "javascript"]
description = "React's render props is easy if you understand FP"
featured = "react.png"
featuredpath = "/img"
featuredalt = ""
title = "Making Sense of React's Render Props"
date = 2019-08-02T09:51:30+03:00
+++

I came across *render props* when checking out Apollo GraphQL's tutorial. Now I think I finally figured it out, I try my best to explain it as easy as possible here in my own words. The highlight is in the last part where I shed a FP light onto it. So stay tuned!

First, stop trying to understand the name *render props*. Instead, look at the following example.

I want to build a React Component which has 2 buttons **cat** and **dog**. When the user click on the **cat** button, it shows the text "cat" somewhere, and by clicking the **dog** button, shows the text "dog".

This should be simple enough, let's *create-react-app* and write the *App.js* as follows:

{{< highlight  javascript>}}
import React from "react";
import "./App.css";

class Animal extends React.Component {
  state = { animal: "Dog" };

  onDogClicked = () => this.setState({ animal: "Dog" });
  onCatClicked = () => this.setState({ animal: "Cat" });

  render() {
    return (
      <div>
        <button onClick={this.onDogClicked}>Dog</button>
        <button onClick={this.onCatClicked}>Cat</button>
        <div>{this.state.animal}</div>
      </div>
    );
  }
}

function App() {
  return (
    <div className="App">
      <Animal />
    </div>
  );
}

export default App;
{{< /highlight >}}

This is like React 101. The `Animal` component has a `state` called `animal`. It has 2 buttons, by clicking them set the state `animal` to "cat" or "dog". And there is a div to display the state in text.

Now thinking about it, it feels a bit boring to just display text, I want to change it to display emojis. I write some code in my head and have to admit that it is not much different from the above, only to replace the `div` with some logic to show a cat or dog emoji based on the state.

Being a professional programmer, I think it only makes sense to create a separate component for displaying the animal state.

{{< highlight javascript >}}
const TextAnimalView = props => <div>{props.animal}</div>;

const EmojiAnimalView = props => (
  <div>{props.animal === "Dog" ? "🐶" : "🐱"}</div>
);

class Animal extends React.Component {
  state = { animal: "Dog" };

  onDogClicked = () => this.setState({ animal: "Dog" });
  onCatClicked = () => this.setState({ animal: "Cat" });

  render() {
    return (
      <div>
        <button onClick={this.onDogClicked}>Dog</button>
        <button onClick={this.onCatClicked}>Cat</button>
        <TextAnimalView animal={this.state.animal} />
        <EmojiAnimalView animal={this.state.animal} />
      </div>
    );
  }
}
{{< /highlight >}}

Now I have `TextAnimalView` to display the animal in text, and `EmojiAnimalView` to display it in emoji. But they are *inside* the `Animal` component. And I really want to be able to pass the *view* into the `Animal` component so that it can use it to display its `animal` state like this:

{{< figure src="/img/render-props-1.jpg" >}}

 *Only if only I know how ...*

The problem here is that the `animal` state is only visible from inside the `Animal` component. If I move `TextAnimalView` and `EmojiAnimalView` outside of it, then how can they access the state `animal`?

*We can play a trick*

As you probably guessed it, to pass the view component into the `Animal` component, it has to go in as a property, sort of.

{{< highlight javascript >}}
<Animal render={<EmojiAnimalView ... />}>
{{< /highlight >}}

But wait, this cannot work, how to pass in the `animal` state?

Here I want to quote from the FP world:

>If you have a problem, you probably can solve it by using a lambda.

Instead of passing the view directly, we can pass in a function that when called returns the view.

{{< highlight javascript >}}
<Animal render={animal => <EmojiAnimalView animal={animal} />}>
{{< /highlight >}}

and we have to change the `render` function in `Animal` accordingly:

{{< highlight javascript >}}
render() {
    return (
      <div>
        <button onClick={this.onDogClicked}>Dog</button>
        <button onClick={this.onCatClicked}>Cat</button>
        {this.props.render(this.state.animal)}
      </div>
    );
  }
{{< /highlight >}}

Aha! The state is passed in by **calling** the render function!

Now you should understand why it is called *render props*.

*One more trick*

Some people want to pass in the view not as a prop but as a child of the `Animal` component, in that case you can also do it like this:

{{< highlight javascript >}}
<Animal>
   {animal => <TextAnimalView animal={animal} />}
</Animal>
{{< /highlight >}}

This can also work because you can still access the child component from the `props`:
{{< highlight javascript >}}
render() {
  return (
    <div>
      <button onClick={this.onDogClicked}>Dog</button>
      <button onClick={this.onCatClicked}>Cat</button>
      {this.props.children(this.state.animal)}
    </div>
  );
}
{{< /highlight >}}


I promised you that the highlight is in the last part, now it comes.

To look at the core of what is happening here, turns out we do not even need react.

{{< highlight javascript >}}
const dog = view => console.log(view("Dog"));
const cat = view => console.log(view("Cat"));
const textView = animal => `This is a ${animal}`;
const emojiView = animal => `This is a ${animal === "Dog" ? "🐶" : "🐈"}`;
dog(textView);
cat(textView);
dog(emojiView);
cat(emojiView);
{{< /highlight >}}

That's it.

Thanks for reading.

Happy coding!

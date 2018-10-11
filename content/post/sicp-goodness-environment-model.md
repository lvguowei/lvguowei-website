+++
categories = ["SICP"]
description = "The environment model of evaluation"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - The Environment Model"
date = 2018-10-08T19:53:20+03:00
draft = true
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Before introducing *assignment operation* `set!`, we can use the **substitution model** of evaluation.

For example, 

{{< highlight scheme >}}
(define (square x) (* x x))

(square 2)
{{< /highlight >}}

To apply the function `square` to the value 2, replace the variable `x`in the body of the function with 2, we get `(* 2 2)`.

Notice that substitution happened here is not equal to *assign x with value 2* in any C like languages. What substitution here means is that x now **is** the value 2. You cannot change x anymore just like you can not change the value 2.

**So before assignment, a variable is merely a name for a value.**

But everything changed after we introduced the assignment operation. Now we have to deal with time and identity and answer the philosophical questions like *Am I the same person if I cut my finger nail?* Or *Can I jump in the same river twice?*

The reason that we still want the assignment is that it somehow helps us to write moduler programs by providing a way to model local state.

But this breaks the old substitution model badly, because a variable is not simply a name of a value, but **a place where a value can be stored in**. So that later on the program can find the place and change its value in it.

The main topic of this article is to understand the new evaluation model: **the environment model.**

Simply put, the **environment** is a chain of **frames**, where a frame is just a mapping from variables to values.

There is one special **global environment** in which contains all the primitive mappings. Things like `cons`, `+` etc.

Let's go through a simple example, consider the procedure definition

{{< highlight scheme >}}
(define (square x) (* x x))
{{< /highlight >}}

evaluated in the global environment.

# Evaluate a procedure

We can think of the evaluation of this function definition in 3 steps.

**Step 1** The procedure definition is just syntactic sugar for an underlying implicit *lambda* expression. The above is equivalent to

{{< highlight scheme >}}
(define square
  (lambda (x) (* x x)))
{{< /highlight >}}

**Step 2** The evaluation of a *lambda* expression will produce a **procedure object**. A procedure object has two parts: the code and the link to its environment.

{{< figure src="/img/environment_model_1.png" >}}

**Step 3** Register in the global environment the name `square` to point to the newly created procedure object.

# Apply a procedure

Now we have a procedure evaluated, let's see how it is applied.

>To apply a procedure to arguments, create a new environment containing a frame that binds the parameters to the values of the arguments. The enclosing environment of this frame is the environment specified by the procedure. Now, within this new environment, evaluate the procedure body.

Let's see an example here.

{{< highlight scheme >}}
(square 5)
{{< /highlight >}}

**Step 1** Create a new environment E1 which has a new frame that has x bound to 5.

**Step 2** This new environment E1 points back at the global environment, because the function `squre` points to global environment.

**Step 3** Evaluate the body of the function `(* x x)` within E1. Which results in 25.

{{< figure src="/img/environment_model_2.png" >}}

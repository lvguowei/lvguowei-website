+++
author = "Guowei Lv"
categories = ["JavaScript"]
keywords = ["JavaScript", "ReactJS"]
description = "A dialog"
featured = "js.jpg"
featuredpath = "/img"
featuredalt = ""
title = "Parenthesis in JavaScript"
date = 2019-08-09T09:58:15+03:00
+++

For JavaScript beginners (I envy all of you because your brains are not damaged by this *yet*), `()` can be a huge confusion.

# Example 1

{{< highlight js >}}
const createPerson = name => {firstname: name};
console.log(createPerson("Guowei"));
{{< /highlight >}}

Of course it logs `undefined`. Let's ask JavaScript what was it thinking when it executed the code.

>**Me**: Hi, JavaScript.
>
>**JavaScript**: Hi!
>
>**Me**: What the heck has happened? Where is my returned object?
>
>**JavaScript**: Wait. What return? What object? I do not see any `return`!
>
>**Me**: ... Alright, I did not write `return` explicitly, but isn't `{firstname: name}` an object? You should just return that I think.
>
>**JavaScript**: There is no object that I see. I thought the `{}` means the mark of the function body!
>
>**Me**: OK, there seems to be some misunderstanding. But if you think again, it makes more sense to treat it as an object, it has `firstname: name`, what do you think of that?
>
>**JavaScript**: Oh, isn't it just a label? Like what people normally do with looping. They have `loop1:` and `loop2:` to mark where to break.
>
>**Me**: ..................
>
>(after 5 minutes)
>
>**Me**: I do not want to continue this conversation, you make no sense.
>
>**JavaScript**: Well, fine. See you then.

P.S. The fix would be to wrap `()` around the returning object like `({firstname: name})`.

# Example 2

If you have done any ReactJs work, you see the following pattern a lot:

{{< highlight js >}}
return (
  <NameCard>
   <FirstName />
   <LastName />
  </NameCard>
);
{{< /highlight >}}

People will normally tell you that when returning multiple lines, just wrap them in `()`. But why? Let's ask JavaScript again.


>**Me**: Hi JavaScript, sorry I was rude last time, but I have a new question.
>
>**JavaScript**: Shoot.
>
>**Me**: Why you cannot return multiple lines of code?
>
>**JavaScript**: Ah! You programmers tend to forget things a lot. E.g. you forget to put `;` after return, so I just put it there for you, you are welcome.
>
>**Me**: .....O......K...., where did you put it by the way?
>
>**JavaScript**: Here `<NameCard>;`.
>
>**Me**: Oh yeah, cool. SAYONARA.(I hope I will never talk to you again)
>
>**JavaScript**: Farewell.

+++
author = "Guowei Lv"
categories = ["Jetpack Compose"]
description = "Learn Jetpack Compose one bite at a time"
linktitle = ""
featured = "compose-promo.png"
featuredpath = "/img"
featuredalt = ""
title = "Bites of Compose 3"
date = 2023-01-27T22:42:03+02:00
+++

Let's do a bit of recap first. (I highly suggest you go through [previous post](https://www.lvguowei.me/post/bites-of-compose-2/) if not already done so)

# Situation 1

Will clicking the button trigger the recompose of `UserPage`?

{{< highlight kotlin >}}

data class User(var name: String)

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val user1 = User("Guowei Lv")
        val user2 = User("Guowei Lv")

        var user = user1

        setContent {
            var flag by remember {
                mutableStateOf(true)
            }
            Column {
                Text(text = "Flag is $flag")
                UserPage(user = user)
                Button(onClick = {
                    flag = !flag
                    user = user2
                }) {
                    Text("Click me!")
                }
            }
        }
    }
}

@Composable
fun UserPage(user: User) {
    Log.d("test", "UserPage recompose!")
    Text("${user.name}'s page")
}

{{< /highlight >}}

---

### Answer

Yes. Everytime. Even if `user1` and `user2` are structurally equal.
The reason is explained in detail in previous article.

# Situation 2

You might have known that Compose has a annotation called `@Stable`, let's try that with out `User` class.

{{< highlight kotlin >}}
@Stable
data class User(var name: String)
{{< /highlight >}}

OK, how about now if we click the button?

---

### Answer
The recomposition of `UserPage` is not happening anymore! Yeah, problem solved. But...really?

Adding the `@Stable` only means that we programmers promise that for any two instances of `User`, if they are equal now, they will be equal forever.
So please Compose, just use structural equality to test two objects and Compose will do so.

But this is a promise that we cannot 100% fulfill. So it's not enough to only add the `@Stable` to `User`, we need more.

# Situation 3
It seems that as long as there is a `var` in `User`'s property, it will be almost impossible to guarantee that.
But there is one thing that we can easily guarantee: the object only equals to itself, which means we don't override its equals() and hashcode() functions,
which means we cannot use `data class`.

{{< highlight kotlin >}}
@Stable
class User(var name: String)
{{< /highlight >}}

At least now our program is more correct!

You will find that no recomposition of `UserPage` is trigger in the following code:

{{< highlight kotlin >}}

@Stable
class User(var name: String)

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val user1 = User("Guowei Lv")
        val user2 = User("Guowei Lv")

        var user = user1

        setContent {
            var flag by remember {
                mutableStateOf(true)
            }
            Column {
                Text(text = "Flag is $flag")
                UserPage(user = user)
                Button(onClick = {
                    flag = !flag
                    // user = user2
                }) {
                    Text("Click me!")
                }
            }
        }
    }
}

@Composable
fun UserPage(user: User) {
    Log.d("test", "UserPage recompose!")
    Text("${user.name}'s page")
}

{{< /highlight >}}

# Situation 4

This is not the whole story. Now let's take a look at the documentation of @Stable:

>Stable is used to communicate some guarantees to the compose compiler about how a certain type or function will behave.
>When applied to a class or an interface, Stable indicates that the following must be true:
>1. The result of equals will always return the same result for the same two instances.
>2. When a public property of the type changes, composition will be notified.
>3. All public property types are stable.

We have talked about guarantee number 1, but here in the doc it is said a bit differently.
It means if two objects are equal then they will be forever equal, and if they are not equal they will never be equal.
According to this, basically we cannot use data classes which has var properties inside.

And rule number 2, in our case, this means whenever the `name` property changes, it has to notify Compose.

Well, this is relatively straightforward to achieve:

{{< highlight kotlin >}}
@Stable
class User(name: String) {
    var name by mutableStateOf(name)
}
{{< /highlight >}}

And rule number 3 are rather easy to understand, no further explanation needed I think.

Actually, Compose only uses rule number 2 to test if the object is stable or not. It is not to say that rule number 1 and 3 are not important, they are and
we should follow them, its just that the Compose compiler is not sophiscated enough to detect those.

So we don't really need the `@Stable` annotation anymore, we can simply write:

{{< highlight kotlin >}}
class User(name: String) {
    var name by mutableStateOf(name)
}
{{< /highlight >}}

And Compose will treat it as stable.

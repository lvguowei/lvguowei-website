+++
date = "2017-03-20T20:27:39+02:00"
title = "Mock Objects Demystified"
tags = ["TDD"]
categories = ["Test Driven Development"]
+++

If you have ever tried writing any non trivial tests, mocks should not be a stranger to you. But what about some other "mock" like objects like stubs, spies and such? How are they different from each other? In this blog post, I will explain it as simple and easy to remember as possible.

Everything is a **test double**. **Test double** is just a general name for all mock like objects.

{{< figure src="/img/test-double.jpeg" >}}

Then the simplest of them all is **Dummy**. A **Dummy** is an implementation of an interface that all methods return as close to nothing as possible. It don't do nothing, it don't return nothing, it's just there.

We often use an dummy when we test a function and it requires some argument that is not related to what we want to test.

{{< figure src="/img/dummy.jpeg" >}}

Next in the sequence of test doubles is **stub**, **stub** is a dummy, all functions in stub do nothing. The difference is that instead of return nothing, the functions return some predefined fixed value.

By return certain values, the stub drives the code to the path way that we want to test.

{{< figure src="/img/stub.jpeg" >}}

The next test double is the **spy**. The spy is a stub, it do nothing and return the value that are useful to the test. However, it remember certain interesting facts about the way it is called, and report the facts later to the test.

Sometimes you write a spy just to remember a function gets called, or how many times it gets called, or how many args are passed in, etc. But, a spy don't do nothing, all the spy does is watch and remember.

{{< figure src="/img/spy.jpeg" >}}

At last, the **mock**. A mock is a spy, but it knows very specific how to check what are being spied. Usually it contains some *verify...()* function like *verifyAlertTriggerOnThirdFailedAttempt()*. So a true mock is a spy knows what to expect, and the test ask the mock if everything went as expected.

{{< figure src="/img/mock.jpeg" >}}

Finally there is the **fake**. A fake is not anything above. It is a simulator. The fake is very smart and complicated and it starts to look like the real deal. For example, a fake `Authorizer` may return `User` with id 1 if you pass in names that start with `good`, else it returns `InvalidUser`. But be caucious not to grow it too much or it will become a mess very quickly. And you probably want to have tests that test fakes. For unit tests fakes are really not that necessary, but for intergration tests, they can come quite handy. But in general, avoid fakes when you can.

{{< figure src="/img/fake.jpeg" >}}

That's the end.

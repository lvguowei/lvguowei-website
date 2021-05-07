+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Multi-Module Components"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Dependency Injection in Android With Dagger2 (6)"
date = 2021-05-03T13:20:50+03:00
+++

Components can have multiple Modules. So if you have huge Modules that provide tons of services, it is good to split it up.

Let's create a `UseCaseModule` to hanle all the `UseCase`s.

{{< highlight kotlin >}}

@Module
class UseCasesModule {

    @Provides
    fun fetchQuestionsUseCase(stackOverflowApi: StackoverflowApi) = FetchQuestionsUseCase(stackOverflowApi)

    @Provides
    fun fetchQuestionDetailsUseCase(stackOverflowApi: StackoverflowApi) = FetchQuestionDetailsUseCase(stackOverflowApi)
}
{{< /highlight >}}

{{< figure src="/img/multi-module-component.png" >}}

Some dagger conventions:
1. Components can use more than 1 module.
2. Modules of the same Component share the same object-graph.
3. Dagger automatically instantiate modules with no argument constructor.


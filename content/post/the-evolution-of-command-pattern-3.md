+++
categories = ["Design and Architecture"]
description = "The Command Pattern Revisited and its implementation in Android Service"
featured = "featured-command.png"
featuredpath = "/img"
title = "The Evolution of Command Pattern (III)"
date = 2017-09-02T11:10:27+03:00
+++

In this final part of the **Command Pattern** series, we will talk about yet another improvement on top of the Command Processor Pattern. It is described in the paper [Command Revisited](/file/command-revisited.pdf). From now on, we will just refer to it as the **Command Revisited Pattern**.

The paper is short and sweet but you might not find it to be to the point after the first glimpse.

The most beautiful part of the **Command Revisited Pattern** is that it provides a new perspective on the general Command Pattern.

> The Command Revisited pattern packages a piece of application functionality as well as its parameterization in an object in order to make it usable in another context, such as later in time or in a different thread.

>We think that Command is basically a way to emulate the concept of closures in object-oriented languages that don't natively have this feature.

This is I think by far the best understanding of the Command Pattern.

### What is closure?

Closure is basically a way to encapsulate function with its execution context, in most cases, the context means a bunch of data. So in short, closure binds function with data. In Object Oriented languages, the Object serves such purpose. In other functional like languages like JavaScript, function serves such purpose. Learn more about how closure is playing a very important role in JavaScript [here](https://developer.mozilla.org/en/docs/Web/JavaScript/Closures).

In order to better understand this pattern, let's go through an example.

# How Android uses the Command Revisited Pattern to implement the Service component

### problem

Synchronous method calls in an Activity can block client for extended periods. Android generates an "Application Not Responding" dialog if an app doesn't respond to user input within a short time. For example. calling a potentially long operation like downloading files or playing music.

### solution

Create a **command processor** that encapsulates a download request as an object that can be passed to a **Service** to execute the request. The process works as follows:

* Implement a `DownloadService` that inherits from Android's `IntentService`.

* Activity creates Intent command designating `DownloadService` as target. The intent should contain the URL and maybe a callback `Messenger` as `extras`.

* Activity calls `startService()` with Intent.

* Activity Manager Service starts IntentService, which spawns internal threads for the download work to run on.

This is almost a perfect map to what is described in the pattern. The `Intent` that is used to start the service can be considered as the `Command`. The worker thread in the `DownloadService` can be seen as the *other time or thread* that the command will be executed in.

### breakdown

Now let's talk about step by step how the android code maps to the pattern.

#### Define an abstract class for command execution that will be used by the executor. You will typically define an `execute()` function.

This is done by the `Intent` class. Notice that here it doesn't follow the pattern exactly, instead of define a hierarchy of Command classes, it uses `extra` fields to define different types of commands.

{{< highlight java >}}
public class Intent implements Parcelable, Cloneable { ... }
{{< /highlight >}}

#### Add the state which the concrete commands need during their execution to the execution context. Make the execution context available to the concrete command.

This is done by putting `Uri` or other types of data as `extra`s in the `Intent`. And later on can be extracted when needed.

#### Define and implement the creator.

This can be done in the activity. When a button is clicked for example, we can create the Intent and call `startService(intent)`.

#### Define the execution context.

The execution context can be seen as the common environment where is kind of shared when the command is created and when it is executed. So we can treat the Android's `Context` as the *context* in the pattern, cause it is accessible both in the Activity and in the Service. E.g. if you want, you can use it to send broadcast in the Service when the work is done, and listening to broadcast in the activity to update the UI.

#### Implement your specific command functionality in subclasses of the abstract command class defined above, implementing the `execute()` operation according to your specific requirements.

In Android, this means to handle different Intent in the Service in `onHandleIntent(Intent intent)` method.

{{< highlight java >}}

public class DownloadService extends IntentService {

    protected void onHandleIntent(Intent intent) {
        if (isDownloadImageIntent(intent)) {
            downloadImage(intent);
        } else if (isDownloadMusicIntent(intent)) {
            downloadMusic(intent);
        }
        ...
    }
}

{{< /highlight >}}

### Consequences

As you can see, the implementation of Android Service component can be almost perfectly matched to the Command Revisited pattern, which provides some **benefits**:

The **creation** of the task, the **scheduling** of the execution of the task, and the **execution** of the task are now completely separated. The task can be even run on a separate process and the client code doesn't need to know it.

There are potential **liabilities** as well, such loose interface means that the implementation is based on contract. It has to be agreed on how to interpret different command on both client and service side. When the system becomes large, it tends to become unclear how to create a command and how to interpret a command.

The pattern doesn't articulate how the callback mechanism works, so we have to put in some other component to handle the communication problem. For example, we can use Broadcast system in Android or some kind of event bus to pass result back from the Service.

This wraps up the whole series of Command Pattern. Hope you enjoyed it. Happy coding.

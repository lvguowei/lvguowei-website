---
title: "Why Synchronize?"
date: 2017-07-01T14:16:31+03:00
tags: ["concurrent programming", "java"]
categories: ["programming"]
---

We are going to write a program, a bit program, lots of stuff happening here and there, ok, big program. Now, lots of threads of course, loads and loads of them, we have to synchronize, yes we do, I think so, yeah, synchronize very important stuff, good idea, but, why?

Remember what we have been taught in school? That **mutex** thing? Mutual Exclusive? That 2 threads are trying to read and write some mutual value at the same time and create a big mess? So that we have to put that wierd **synchronized** keyword all over the place try to help it? Here is an example:

Support we are trying to generate some auto increasing IDs,

{{< highlight java >}}

class AutoIncrementID {

    private static long id = 0L;

    static long gen() {
        long prev = id; // read

        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        id = prev + 1; // write
        return id;
    }
}

{{< /highlight >}}

In this example, we read the value, we increase it by 1, and then we write it back. Not atomic at all. Notice that the *sleep* there is to make it more likely to be inconsistant while we run it with multiple threads.

If we call *gen()* within two threads, like this:

{{< highlight java >}}

public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                System.out.println(AutoIncrementID.gen());
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                System.out.println(AutoIncrementID.gen());
            }
        });

        t1.start();
        t2.start();
    }

{{< /highlight >}}

We will have duplicated IDs. To fix it, just add **synchronized** keyword to the **gen()**.

So rule number 1, **if there are read and write at the same time, meaning the operation is not atomic, then we need synchronization**.

But, this is **NOT** the end of the story. Even if the operation is atomic, we sometimes still need to synchronize. Take a look at the following example:

{{< highlight java >}}
public class StopThread {
    private static boolean stopRequested;

    public static void main(String[] args)
            throws InterruptedException {
        Thread backgroundThread = new Thread(new Runnable() {
            public void run() {
                int i = 0;
                while (!stopRequested)
                    i++;
            }
        });
        backgroundThread.start();
        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;
    }
}
{{< /highlight >}}

This program may never end. Why? Because the change to **stopRequested** in the main thread may not be seen in the background thread. What is the fix? Add **volatile** to **stopRequested** to make sure changes from one thread will properly propogates to other threads.

This is rule number 2, **Synchronization is required for reliable communication between threads**.

~The End~

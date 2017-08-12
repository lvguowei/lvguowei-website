+++
categories = ["Emacs"]
description = "How to use Magit in Emacs to do bisecting"
featured = "featured-bisect.png"
featuredpath = "/img"
featuredalt = "Git rebase with Magit"
title= "Magit Tutorial - Bisect"
date= 2017-08-12T08:07:00+03:00
+++

It's 5 o'clock on a sunny Friday afternoon, you are thinking where to have dinner with friends and such. The only thing you have to finish before you can go is to pull your colleage's latest changes and make a demo build to customer. You pull, there are 20 commits all nicely structured, you are now giving yourself a pat on the back for teaching him to use rebase yesterday (by pointing him to these 2 wonderful articles [1](https://www.lvguowei.me/post/magit-rebase/) and [2](https://www.lvguowei.me/post/magit-rebase-2/)). The build is making, the clock is ticking, you are a little bit late to the dinner party. You installed the app, and it quits right after you open it, leaves no trace at all in the log. Your heart sinks.

This happens to every developer. What? it never happened on you? It will, you can count on me, and when it happens, it will be on Friday afternoon most likely. So what should you do? You can check out each and every single commit and run the build, install it and see if the problem remains. But you are a good programmer, you know binary search by heart, so you take out a piece of paper, and jot down the commits you tested and the results, and you can quickly pin down the last working commit, and make a build from there. Actually git has a way to help you do the exact same, it is called [bisect](https://git-scm.com/docs/git-bisect). Let's try it out.

Let's first take a look at the crime scene.

{{< figure src="/img/bisect-init.png" >}}

What we know is that the last commit `add 10` is bad, and the first commit `init` is good. So we need to find out the first bad commit in the whole history.

So now, press `B`(Bisecting) `B`(Start), then it will prompt you with *Start bisect with bad revision:*
Enter `HEAD` as the first bad version, and `HEAD~10` as the last good version.

Now we have set the range, Magit shows this now:

{{< figure src="/img/bisect-1.png" >}}

This shows that we are currently at commit `add 5`, so we can test to see if this is a bad commit now. Let's open the `file.txt` and it looks perfect normal:

{{< highlight sh >}}
0
1
2
3
4
5
{{< /highlight >}}

OK, we can now mark this commit as good. Press `B`(Bisecting) `g`(Good).

Now Magit shows as this:

{{< figure src="/img/bisect-2.png" >}}

Now we are at commit `add 7`, let's check `file.txt` and it shows:

{{< highlight sh >}}
0
1
2
3
4
5
6
*7
{{< /highlight >}}

AHA! There it is! There is an extra * there. OK, we found the problem, we can now mark this commit as bad by pressing `B`(Bisecting) `b`(Bad).

Then next screen looks like this:

{{< figure src="/img/bisect-3.png" >}}

OK, now we can check commit `add 6`. Believe me, it looks normal. So we mark it as good as well.

Done! There goes the verdict screen:

{{< figure src="/img/bisect-verdict.png" >}}

As you can see, I did commit a bad * there with number 7.

Phew! We can now fix it. But wait, this still feels like a lot of manual work. Can we even automate it more? The anwser is yes, so let's do this all over again. You can reset everything by `B` `r`(Reset).

You maybe already noticed that there is a `script.sh` there, it turns out that instead of manually check the good or bad of a certain commit, we can run a script to do it. If the script returns 0 it means good commit, if it is non-zero then it is a bad commit.

Given all that, it is obvious that we can write a script like this:

{{< highlight sh >}}
count=$(grep -c "*" file.txt)
exit $count
{{< /highlight >}}


Now we can do the bisect by first press `B` then `s`(Start script). Then just follow the instruction to set the first bad commit and last good commit, then a command to run the script. And the result should be the same.

{{< figure src="/img/bisect-script.png" >}}

~THE END~

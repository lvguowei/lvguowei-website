+++
categories = ["Emacs"]
description = "How to use Magit in Emacs to do rebase in Git"
featured = "featured-rebase.png"
featuredpath = "/img"
featuredalt = "Git rebase with Magit"
title= "Magit tutorial - Rebase (Part II)"
keywords= ["Magit", "Git", "Emacs", "Rebase"]
date= 2017-08-06T09:30:26+03:00
+++

In [part I](https://www.lvguowei.me/post/magit-rebase/) we went through how to use git rebase to modify commit history, things like reword a commit, squash multiple commits, split commit. In this part, we will talk about another common use case of rebase: rebase before merging branches.

Let's first create a new repo and add one `file.txt` as the first commit.

{{< highlight sh >}}
apple
pear
peach

cat
dog
snake
{{< /highlight >}}

Now we create a `feature` branch from `master` branch. To create a new branch, press `b`(Branching) and `c`(Checkout new branch).

Now that we are on the `feature` branch, lets add a new file `file2.txt` with the folloing content:

{{< highlight sh >}}
TODOs:
1. Go to supermarket
2. Pick up dog
{{< /highlight >}}

Commit the changes with message `add todos`.

Then let's modify the `file.txt` to the following:

{{< highlight sh >}}
apple
pear
peach

cat
dog
pig
{{< /highlight >}}

Then commit with the message `change snake to pig`.

Now let's switch back to `master` branch.

To make things more interesting, let's also modify the `file.txt` to replace `snake` with `panda`, then commit.

To recap, now the history looks like this:

{{< figure src="/img/before-rebase.png" >}}

Note that the last commits in `master` and `feature` are conflicting each other.

Suppose now the `feature` branch has finished, and we want to merge it back to `master`.

First, let's rebase it against master.

Checkout `feature`, and press `r`(Rebasing), and `e`(elsewhere) and choose `master`. Since there is conflict, Magit will show the following page indicating that we have to solve the conflicts.

{{< figure src="/img/rebase-conflict.png" >}}

This is as we expected, let's now use the `Ediff dwimming`(do what I mean) to resolve the conflict. Put the cursor on the conflicting file, and press `e`.

Now we are entering the `Ediff` buffer.

{{< figure src="/img/ediff.png" >}}

Let's say that we decided to take the changes in `feature` branch, then we can press `n` to select the diff, and `b` to choose variant B. If everything goes right, we should see that the C Section should now contain the correct text.

{{< figure src="/img/ediff-ok.png" >}}

Now press `q` to quit `Ediff` and also choose save the file when prompted.

Now we should be back at the main Magit screen, press `g` to refresh should show the following.

{{< figure src="/img/ediff-done.png" >}}

Looks everything is good, we can press `r` and continue rebasing.

After done, the log should show the following:

{{< figure src="/img/rebase-done.png" >}}

See that now the history looks very clean and as if changes were done in a linear fashion.

~The End~

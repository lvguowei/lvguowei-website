+++
categories = ["Emacs"]
description = "How to use Magit in Emacs to do rebase in Git"
featured = "featured-rebase.png"
featuredpath = "/img"
featuredalt = "Git rebase with Magit"
title= "Magit tutorial - Rebase (Part I)"
keywords= ["Magit", "Git", "Emacs", "Rebase"]
date= 2017-08-05T15:25:20+03:00
+++

[**Magit**](https://magit.vc/) is arguably the best Git tool out there and also my favorite. It is a package in Emacs, and it is text based. In this tutorial, we will explore how to use it to tackle one of the more elusive topics in Git: **Rebase**.

# In the beginning there was darkness
Let's create an empty repository and add one empty file to it.

{{< highlight sh >}}
git init
touch file.txt
git add .
git commit -m 'first commit'
{{< /highlight >}}

# Rewording commit
Now let's add some fruits in the file. Open the file and add a new line

{{< highlight sh >}}
apple
{{< /highlight >}}

Open Magit, commit the change with a message says

{{< highlight sh >}}
Add apple
{{< /highlight >}}

Then add a new line

{{< highlight sh >}}
pear
{{< /highlight >}}

with commit message
{{< highlight sh >}}
apple again, wrong commit message
{{< /highlight >}}

Then add a new line
{{< highlight sh >}}
peach
{{< /highlight >}}

with a commit message
{{< highlight sh >}}
peach
{{< /highlight >}}

Now it is clear that we need to fix the second commit message. But since this is not the last one we cannot use `git commit --amend`. But we can still use `rebase` to change it.

In Magit, press `ll` to open the log history. Put the cursor under the wrong commit.

{{< figure src="/img/select-wrong-commit.png" >}}

Then press `r`(rebase) and `w`(to reword a commit).

{{< figure src="/img/ready-to-reword.png" >}}

Now enter the correct commit message *pear* and press `C-c C-c`. Now you should see the commit messages updates.

# Squashing commits

It appears that the last 3 commits should really be 1 commit with a message like *add fruits*.
We can use rebase to squash them into one commit like this:

In the commit history, put the cursor on the oldest of the 3 commits that we want to squash, and press `r`(rebase) `i`(interactively). We now see the interactive rebase page.

{{< figure src="/img/rebase-interactive-page.png" >}}

The good thing about this page is that it contains a cheatsheet already, so you can just see what kind things can be done here.

Notice that the order of the commits now is reversed, the latest commit being at the bottom.

As the cheatsheet says, we can put the cursor on the *pear* commit and press `s`, this means that we want this commit to be squashed into its previous commit. And then we do the same for the `peach` commit.

{{< figure src="/img/squash-page.png" >}}

We can see that the two commits are marked as `squash` now, then we can press `C-c C-c` to make the squash happen. It will prompt you to enter the new commit and also hint you the previous commit messages. Type *Add fruits* and press `C-c C-c`.

Now we can see from the history that the 3 previous commits become one now.

{{< figure src="/img/squashed-commits.png" >}}

# Splitting commit

It will come at times that we want to split a big commit into smaller ones. This can also be achieved by using rebase.

Let's add a new file `file2.txt` and modify the `file.txt` to add some animal names.
Commit the changes with message *add animals to file and create file2*. So we want to split this commit into 2 commits, one for adding animals and one for creating *file2*.

As always, we go to the commits history page, put the cursor on the last commit, and press `r`, then press `m`(to edit a commit).

{{< figure src="/img/edit-commit.png" >}}

Notice that there is a `@` sign in front of the commit, meaning the HEAD now is at this commit.
And then, we want to move the HEAD one step before the current by git reset. Move the cursor to previous commit *Add fruits* and press `x`, then choose `master~1`. Now go back to the main screen you should see this:

{{< figure src="/img/split-reset.png" >}}

Now you can commit the changes separately.

{{< figure src="/img/split-done.png" >}}

Now continue the rebase by click `r` again and choose `r`(continue).

OK, now the split is done. Check the history you should see the expected result.

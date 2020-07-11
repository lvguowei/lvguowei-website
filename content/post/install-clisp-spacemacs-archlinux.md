+++
author = "Guowei Lv"
categories = ["Common Lisp"]
description = "How to setup CLISP environment in Spacemacs on Archlinux"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Setup CLISP Environment for Land of Lisp"
date = 2020-07-11T22:39:47+03:00
+++

The famous Land of Lisp uses CLISP. This is a tutorial to help you setup everything up.

I use Spacemacs + Common Lisp Layer + CLISP + Manjaro Linux.

After install the Common Lisp Layer in Spacemacs, when start up Slime, I got an error saying: could not load ASDF.

Here is the fix that worked for me:

Install asdf separately: `yay asdf`

After it is successfully installed, it prints:
`To load ASDF every time you start Lisp, copy the lines from /etc/default/asdf to your ~/sbclrc or ~/.ccl-init.lisp`

In my case, I put what is inside /etc/default/asdf into `~/.swank.lisp`.

Now things should start to work.

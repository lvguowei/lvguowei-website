+++
author = "Guowei Lv"
categories = ["SICP"]
keywords = ["SICP", "Racket", "Org-mode", "mit scheme"]
description = "How to setup orgmode to run racket code from SICP"
featured = "featured-sicp.jpg"
featuredpath = "/img"
title = "SICP Goodness - Orgmode + Racket + SICP = 💕"
date = 2020-08-06T23:10:10+03:00
+++

>Do you think Computer Science **equals** building websites and mobile apps? 

>Are you feeling that you are doing **repetitive** and **not so intelligent** work?

>Are you feeling a bit **sick** about **reading manuals** and **copy-pasting** code and keep **poking around** until it works **all day long**? 

>Do you want to understand the **soul** of **Computer Science**?

>If yes, **read SICP!!!**

Recently, I started to write down study notes in one org file. And I think why not write the scheme code from SICP in org mode too. But there is a problem: ob-scheme does not work well with mit scheme, that means `C-c C-c` does not execute the code block and throws some error instead.

After some research I found that I can use Racket + sicp package.

1. [install Racket](https://docs.racket-lang.org/pollen/Installation.html).
2. Then [install sicp package through DrRacket](https://docs.racket-lang.org/sicp-manual/)
3. Install `ob-racket`
{{< highlight elisp >}}
dotspacemacs-additional-packages '(
  ; ...
  (ob-racket :location
            (recipe :fetcher github :repo "DEADB17/ob-racket"))
)
{{< /highlight >}}
4. Configure it by adding the following code.
{{< highlight elisp >}}

(defun dotspacemacs/user-config ()
  ; ...
  (use-package ob-racket
    :after org
    :pin manual
    :config
    (append '((racket . t) (scribble . t)) org-babel-load-languages))
)
{{< /highlight >}}

{{< figure src="/img/sicp-org.png" >}}


Enjoy!


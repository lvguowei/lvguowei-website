+++
author = "Guowei Lv"
categories = ["emacs"]
description = "How to setup orgmode pdf export with minted on Arch Linux"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Emacs Orgmode Minted Setup"
date = 2020-05-28T23:58:25+03:00
+++

# Install minted package
https://en.wikibooks.org/wiki/LaTeX/Installing_Extra_Packages

# Install Pygments
https://pygments.org/

# Configure Emacs

{{< highlight elisp >}}
(setq org-latex-listings 'minted
      org-latex-packages-alist '(("" "minted"))
      org-latex-pdf-process
      '("pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"
        "pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"
        "pdflatex -shell-escape -interaction nonstopmode -output-directory %o %f"))
{{< /highlight >}}

# Remove cached _minted_* dirs
This is the step that unblocked me.

# Prevent src block line too long
{{< highlight elisp >}}
(setq org-latex-minted-options '(("breaklines" "true")
                                 ("breakanywhere" "true")))
{{< /highlight >}}

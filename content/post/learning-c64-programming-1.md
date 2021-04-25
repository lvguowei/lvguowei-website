+++
author = "Guowei Lv"
categories = ["C64"]
description = "Assembly and Machine Code Programming on C64"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "Learning C64 Programming 1"
date = 2021-03-06T22:14:18+02:00
+++

The old is the new new.

I really love 8-bit games, why not try to learn how to develop them? The best time to do this is when I was 10 years old after I got my first NES console, the second best time is now. So here we are, I'm documenting all the learning along the way in this blog.

First, let's setup the dev environment.

# Machine

Here is the machine that I'm using for this, my newly acquired ThinkPad T60, with Zorin 32bit version installed.

{{< figure src="/img/thinkpadt60.jpg" >}}

# Development Tools

I decided to go absolute bare bone on the tools:

1. [Kick Assembler](http://theweb.dk/KickAssembler/Main.html#frontpage) 
2. [Vice](https://vice-emu.sourceforge.io/)
3. Emacs

Kick Assembler is quite easy to setup, as all there is to it is a jar file.

Vice can be install directly by `sudo apt install vice`, but there is some problem with installing this way.
The emulator cannot be started because it complains that some "Rom" is missing. There is a solutions to this:
download the Vice for Windows and copy paste some files from C64 folder. But I don't want to do this.
So instead I just downloaded the source code and compiled myself, and I'm happy with the result. (The step by step
installation instructions is included in the zip file with the source code.)

Now let's test out if all is working, using the example comes with the Kick Assembler.

Go to `KickAssembler/Examples/01.MusicIrq`.

Run `<PATH TO KICKASSEMBLER.JAR> MusicIrq.asm`.

See that the `MusicIrq.prg` is generated.

Then run `<PATH TO X64 EXE> MusicIrq.prg`.

And Ta-Da!

{{< figure src="/img/c64demo.png" >}}

Even though we have setup the Kick Assembler, we will be programming "inside the C64" for quite some time,
to learn the basics of how everything works.

Stay tuned!


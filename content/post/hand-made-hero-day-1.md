+++
author = "Guowei Lv"
categories = ["Hand Made Hero"]
description = "A following along for HMH day 1"
featured = ""
featuredpath = ""
featuredalt = ""
title = "Hand Made Hero Day 1"
date = 2018-12-06T12:20:41-08:00
+++

# Project Setup

Use the command `subst` to create an alias to the working directory:

`subst w: C:\Users\lv\work`

Our project is actually created inside the `work` directory like this: `\work\handmadehero`.

Inside `handmadehero`, create two folders `misc` and `code`.

Inside `code`, create file `win32_handmade.cpp`.

# Windows Entry Point

All windows program has a entry point as follows, just put it in our code.

{{< highlight c >}}

int CALLBACK WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance,
                     LPSTR lpCmdLine, int nCmdShow) {
  return 0;
}

{{< /highlight >}}

# How to Build

We build our project from command line. The compiler in Windows we are using is called `cl`. But it is not on the path by default. So you have to exec a bat file to make it usable.

{{< highlight shell >}}

@echo off
call "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
set path=w:\handmadehero\misc;%path%

{{< /highlight >}}

Configure the cmd to automatically call this at start up by modifying the `target` in properties to

`%windir%\system32\cmd.exe /k W:\handmadehero\misc\shell.bat`

Now close and reopen the cmd and you should be able to call `cl`.

We also can lauch emacs from cmd.

Create a `emacs.bat` in misc folder:

{{< highlight shell >}}
@echo off
call "C:\Program Files\emacs\bin\runemacs.exe"
{{< /highlight >}}

Since `misc` folder is in the path, you should now be able to type "emacs" to lauch it.

Let's create a `build.bat` inside `code`.

{{< highlight shell >}}
@echo off
mkdir ..\..\build
pushd ..\..\build
cl -Zi ..\handmadehero\code\win32_handmade.cpp
popd
{{< /highlight >}}

Make a `data` directory in `handmadehero` to put all the things needed for the game.

Now launch the VisualStudio by calling

`devenv w:\build\win32_handmade.exe`

Right click on the solution, and change the working directory to the new data directory.


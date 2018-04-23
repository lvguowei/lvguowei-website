---
title: "How To Follow Hand Made Hero On Linux"
date: 2017-07-20T09:06:31+03:00
categories: ["Hand Made Hero"]
---

If you are a Linux person and you want to follow the awesome [Hand Made Hero](https://handmadehero.org/), here are how I am doing it(on Arch Linux).

1. Install [VirtualBox](https://www.virtualbox.org/wiki/VirtualBox).
2. Create a Windows virtual machine(at least Win7 + SP1, you can download a ISO image from [here](http://mirror.corenoc.de/digitalrivercontent.net/)), pay attention to the hard drive size, 25G is too small, increase it to like 50G at least.
3. In order to make the full scren mode work, we have to install something called *Guest Addition*. Go to *Devices -> Insert Guest Additions CD Image...*, then follow the instruction to download and install it. After it's done, Full Screen Mode should work now.
4. Install *Visual Studio 2017 Community*, it may prompt you to install latest *.NET Framework*, just follow the instructions.
5. 如果你想激活，可以使用小马win7 激活工具，亲测可用。
6. To make the Visual Studio C++ compiler work, we have to run a script called `vcvarsall.bat`. The script location on Visual Studio 7 Community Edition is: `C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvarsall.bat`. But, in order to call it, we have to give a parameter `x86` for 64bit system. You can also write `call "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvarsall.bat" x64` in a bat file.

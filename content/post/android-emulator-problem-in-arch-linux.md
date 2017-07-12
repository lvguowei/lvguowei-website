---
title: "Android Emulator Problem In Arch Linux"
date: 2017-07-12T14:13:07+03:00
tags: ["Android", "AndroidStudio", "Android Emulator", "Arch Linux"]
categories: ["misc"]
---

I don't remember since when, but now whenever I upgrade Android Studio from pacman, I cannot open my emulator, with some `libGL error: unable to load driver: i965_dri.so` error.

I tried to follow the solution online but found none of them is working out of the box, because Android Sdk changed some paths. Here is what actualy works now:


1. cd ANDROID_SDK_PATH/emulator/lib64/libstdc++/
2. mv libstdc++.so.6 libstdc++.so.6.bak
3. ln -s /usr/lib64/libstdc++.so.6 libstdc++.so.6

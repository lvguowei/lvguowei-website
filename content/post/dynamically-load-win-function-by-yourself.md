+++
categories = ["Hand Made Hero"]
keywords = ["HandMadeHero", "Windows Programming"]
description = "How to load Windows function by yourself"
featured = "hmh.png"
featuredpath = "/img"
title= "Dynamically Load Windows Function by Yourself"
date= 2018-01-07T21:01:21+02:00
+++

In HandMadeHero Day6, Casey showed a neat way to load a Windows function by yourself. The senario is as follows:

In order to get game pad input working, we need the function `XInputGetState`, but it is in different `dll` files on different Windows versions, if we link directly to `Xinput.lib`, then we have the risk of the program won't start if somehow the dll is not found in the system. We don't want that to happen considering that the user may not even use a game pad.

So basically we want to have a stub version of the particular function in place if we couldn't find the real one. Here is the code:

{{< highlight c >}}

#include <stdint.h>
#include <windows.h>
#include <xinput.h>

#define global_variable static

#define X_INPUT_GET_STATE(name)                                                \
  DWORD WINAPI name(DWORD dwUserIndex, XINPUT_STATE *pState)

typedef X_INPUT_GET_STATE(x_input_get_state);
X_INPUT_GET_STATE(XInputGetStateStub) { return (0); }

global_variable x_input_get_state *XInputGetState_ = XInputGetStateStub;

#define XInputGetState XInputGetState_

void Win32LoadXInput(void) {
  HMODULE XInputLibrary = LoadLibrary("xinput1_3.dll");
  if (XInputLibrary) {
    XInputGetState =
        (x_input_get_state *)GetProcAddress(XInputLibrary, "XInputGetState");
    if (!XInputGetState) {
      XInputGetState = XInputGetStateStub;
    }
  }
}

int CALLBACK WinMain(HINSTANCE Instance, HINSTANCE PrevInstance,
                     LPSTR CommandLine, int ShowCode) {
  Win32LoadXInput();
  return (0);
}

{{< /highlight >}}

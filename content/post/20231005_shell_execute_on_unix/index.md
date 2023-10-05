---
title: "How DotNet Process Shell Execute works on Linux"
description: "There is such a thing."
date: 2023-10-05T20:05:03+08:00
categories:
    - Programming
tags:
    - ad-blocker
    - privacy
---

When you start a process with `UseShellExecute` set to `true` on Windows, .NET Runtime calls the Win32 [ShellExecute](https://learn.microsoft.com/windows/win32/api/shellapi/nf-shellapi-shellexecuteexw) API to have the OS do this for the application.

However, on GNU/Linux, the things work different. First, the .NET Runtime will check if your target file is executable. If so, the .NET Runtime will start it as an executable.

If the target file is _not_ an executable, then .NET will attempt to find the following external commands in `PATH`: `xdg-open`, `gnome-open` and `kfmclient`. The first available external command of the above will be executed to start the program with default association.

What if none of these are available? The .NET Runtime will throw a `Win32Exception` stating that it failed to create the process because "No such file or directory".

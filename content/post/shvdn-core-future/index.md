---
title: ".NET Hooks for GTA V: .NET 5+, Brief History (assumed) and future"
description: A page about .NET 5+ possiblity for .NET loaders, hooks, etc., and all other techinical difficulities around these projects
date: 2022-12-21T12:01:01+08:00
image: cover.png
categories:
    - Programming
tags:
    - gtav
    - scripthookv
    - dotnet
    - scripthookvdotnet
---

> NOTE
>
> This article is not affliated with Script Hook V .NET project, nor the RAGE Plugin Hook project.
> Pronouns avoided to avoid controversy. All credits for quotes, projects and resources mentioned below goes to their respective authors and contributors.

Many of you may already heard of the [Community Script Hook V .NET](https://github.com/crosire/scripthookvdotnet) project. This is basically a continuation of the original [Script Hook IV .NET](https://github.com/HazardX/gta4_scripthookdotnet) project (note that the IV is to differ from V). Then, there is the [RAGE Plugin Hook](https://discord.gg/ragepluginhook)[^1], mainly for LSPDFR project.

However, some of really tech savvy users could have spotted an issue - **None of these supports new .NET 5+**! Yes, GTA V projects above absolutely are aware of issue and these have not aware have already outdated and abondoned.

Below is a brief history related to .NET family and particular problems.

## The .NET Framework

Back then, when these projects started, .NET Framework is the choice. And at that time, .NET Core either have not came out or is too unmature to be used, and everyone have or had .NET Framework runtime on their computer, or they can easily install it. Most hooks take advantage of `C++/CLI` language feature to integrate C++ hooks with .NET Framework or hook the game completely on its own.

But there is one catch! `C++/CLI` was designed to merely **Call C/C++ functions from C# easier**, and did not support **hosting C# code from C/C++** which script hooks require. So, these projects have to use all sorts of hacky ways to get it up and running.

> Originally SHVDN did not handle this at all, it just called managed functions from the unmanaged SHV callback directly. This was fine as long as the stack space did not change between callbacks, which was the case most of the time. Unfortunately some actions like loading a save game cause SHV to reload SHVDN and it does so with a different fiber and therefore different stack space then before. The next call into managed code therefore generally crashed pretty quickly. My "solution" back then (bf12d5c, like 6 years ago, don't remember the details anymore to be honest) was to ensure SHVDN always calls into managed code from the same fiber. This way on the first managed call the CLR sets itself up in the fiber's stack space and consequent calls are always guaranteed to hit that same space. I did not dig into it much further back then since it seemed to get rid of the problem at hand. The core of SHVDN hasn't really changed all that much since and nobody looked at it again, so the "fix" stuck.
>
> -- crosire in [crosire/scripthookvdotnet#976](https://github.com/crosire/scripthookvdotnet/issues/976)

## The new .NET

However, the situtation more or less have changed as the .NET 5 became **The .NET**, it supports Windows desktop (and not to mention GNU/Linux and Mac), [runs](https://softwarehut.com/blog/tech/net-core-vs-net-framework-testing-performance) [faster](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-5/), have new and faster APIs, supports new C# language versions and features and even more!

Anyway, after .NET 6 LTS release, I [asked on the LSPDFR Forum](https://www.lcpdfr.com/forums/topic/119555-why-script-hooks-and-mod-frameworks-for-gta-still-uses-net-framework/) on January about this particular issue, where one of the developers who works with LSPDFR and RPH team responded:

> There is no support for AppDomains in .NET as compared to framework, so isolating plugins is a very different beast. I suppose the way forward would be using IPC as isolation, but I don't think anyone has looked into that yet for a GTA framework.
>
> -- LMS, in [this reply](https://www.lcpdfr.com/forums/topic/119555-why-script-hooks-and-mod-frameworks-for-gta-still-uses-net-framework/?do=findComment&comment=706905)

In this reply, LMS pointed out that a big part of reason why RPH does not update to .NET 5+ is that isolation and security boundaries for `AppDomain` have been axed:

> On .NET Core, the AppDomain implementation is limited by design and does not provide isolation, unloading, or security boundaries. For .NET Core, there is exactly one AppDomain. Isolation and unloading are provided through AssemblyLoadContext. Security boundaries should be provided by process boundaries and appropriate remoting techniques.
>
> [AppDomain on Microsoft Learn documentation](https://learn.microsoft.com/en-us/dotnet/api/system.appdomain?view=net-7.0#remarks)

However, there are ways to isolate on .NET, as pointed out by LMS and in the docs, is to use interprocess communication and use process boundaries to isolate plugins and scripts.

## Conclusion, and Forward

There are attempted [SHVDN porting efforts](https://github.com/crosire/scripthookvdotnet/pull/1118). If you want to work on it you can.GTA V Modding frameworks, at this stage, should stay on .NET Framework for more couple of years as when either .NET Framework axed or support dropped by Microsoft or GTA VI have came out (which still 2-3 years away at least). However I doubt new technology would be used if .NET Framework is still supported when GTA VI came out.

[^1]: This link goes to the RPH Discord. RAGE Plugin Hook original author is "away without official leave" for as least a year now and the latest available RPH on that website is still for game build 2372! you can try it [here](https://ragepluginhook.net)
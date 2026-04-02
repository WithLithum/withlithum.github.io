---
title: "·NET Script Hooks: Pathway onto the new runtime"
description: A rather short blog about how we got here and what's next.
date: 2026-04-02T17:16:00+08:00
image: cover.png
categories:
    - Programming
tags:
    - gtav
    - scripthookv
    - dotnet
    - scripthookvdotnet
---

> [!NOTE]
>
> This article was originally published in 2022, and have been revisited in April 2026.

In the Grand Theft Auto V modding scene, script mods are usually written in C# or Visual Basic, with the [Community Script Hook V .NET](https://github.com/scripthookvdotnet/scripthookvdotnet), which can be seen as a spiritual successor of [Script Hook IV .NET](https://github.com/HazardX/gta4_scripthookdotnet). G17 Media have also invented [RAGE Plugin Hook](https://discord.gg/ragepluginhook)[^1], which is mainly used for the LSPD First Response community.

They are made with the .NET Framework in mind, which have been semi-active recently and Microsoft has been encouraging migration to the new .NET (originally .NET Core) since .NET 5 released.

Below is a brief history on what's stopping them migrating to .NET.

## The .NET Framework

When these projects started, if you say .NET, you mean the .NET Framework, which is the original Windows framework. At that time, .NET Core have not matured yet, and everyone have or had .NET Framework runtime on their computer, or they can easily install it.

Most hooks are written in, or at least partially written in the `C++/CLI` language. This is a technology that allows you to use C++ to integrate .NET Framework applications with existing C++ code. It _supports_ the modern .NET but only on Windows.

To integrate with Grand Theft Auto V, the hooks injects themselves into the game then loads the scripts. The complexity of the RAGE scripting virtual machine often also requires the hooks to handle [unique threading problem](../ticks-and-loops-in-gta-v-scripting/) that it creates.

### Script Hook V .NET

To add .NET scripting, SHVDN chose to integrate with the existing Script Hook V. The problem? It was _never designed_ to run managed code, so it has various techniques on disposal that only supported native code. This have caused a few serious problems:

{{< quote author="crosire" source="scripthookvdotnet #976" url="https://github.com/crosire/scripthookvdotnet/issues/976">}}
Originally SHVDN did not handle this at all, it just called managed functions from the unmanaged SHV callback directly. This was fine as long as the stack space did not change between callbacks, which was the case most of the time.

Unfortunately some actions like loading a save game cause SHV to reload SHVDN and it does so with a different fiber and therefore different stack space then before. The next call into managed code therefore generally crashed pretty quickly.

My "solution" back then (bf12d5c, like 6 years ago, don't remember the details anymore to be honest) was to ensure SHVDN always calls into managed code from the same fiber. This way on the first managed call the CLR sets itself up in the fiber's stack space and consequent calls are always guaranteed to hit that same space. I did not dig into it much further back then since it seemed to get rid of the problem at hand. The core of SHVDN hasn't really changed all that much since and nobody looked at it again, so the "fix" stuck.
{{< /quote >}}

That particular one caused RAGE Script Hook to crash on some configurations, and it was finally [fixed by this PR in 2023](https://github.com/scripthookvdotnet/scripthookvdotnet/pull/1286).

### RAGE Plugin Hook

For some reason, developers at G17 Media had a different perspective. Instead of managing individual subclasses of `Script`, why not make custom script mods looks like ordinary programs?

Behold, RAGE Plugin Hook. It is used to develop LSPD First Response, although it has limited use outside of that particular community. Its threading model allows developers to painlessly develop plugins at larger scale.

## The new .NET

So Microsoft releases .NET 5, and coined the previously-known-as .NET Core as the future of .NET, supporting all major operating systems, [runs](https://softwarehut.com/blog/tech/net-core-vs-net-framework-testing-performance) [faster](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-5/). New framework features are developed exclusively for this new .NET implementation.

### Porting script hooks

Sometime after the .NET 6 LTS release, I [asked on the LSPDFR Forum](https://www.lcpdfr.com/forums/topic/119555-why-script-hooks-and-mod-frameworks-for-gta-still-uses-net-framework/) on January 2022 about this particular issue. LMS, the person who works on LSPD First Response and RAGE Plugin Hook, expressed why porting the plugin hook is not yet possible:

{{< quote author="LMS" source="LCPDFR.com" url="https://www.lcpdfr.com/forums/topic/119555-why-script-hooks-and-mod-frameworks-for-gta-still-uses-net-framework/?do=findComment&comment=706905">}}
There is no support for AppDomains in .NET as compared to framework, so isolating plugins is a very different beast. I suppose the way forward would be using IPC as isolation, but I don't think anyone has looked into that yet for a GTA framework.
{{< /quote >}}

If you do not know, `AppDomain` provided _runtime-level security and isolation_ for managed code, which was slowly being deprecated because it was inferior to just using processes and created more problem that what it was worth.

So Microsoft axed the isolation part:

> On .NET Core and .NET 5+, the AppDomain implementation is limited by design and does not provide isolation, unloading, or security boundaries. These versions have exactly one AppDomain. Isolation and unloading are provided through AssemblyLoadContext. Security boundaries should be provided by process boundaries and appropriate remoting techniques.
>
> [AppDomain on Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.appdomain)

Script Hook V .NET, on the other hand, is much more lucky than RPH in terms of architecture; it did not have isolation, and there is [already a working example](https://github.com/Sardelka9515/scripthookvdotnetcore). Sadly, it is [unmaintained](https://github.com/Sardelka9515/scripthookvdotnetcore/issues/10#issuecomment-1909193801) and I doubt it still works on the latest version.

The main project, on the other hand, has [given up on supporting modern .NET](https://github.com/scripthookvdotnet/scripthookvdotnet/issues/1036#issuecomment-1595960871) for the current codebase. The maintainers have decided that it would be more practical to create a new script hook rather than trying to port the old code.

## Conclusion, and Forward

There is currently no maintained Script Hook V .NET port for Grand Theft Auto V.

I predict script hooks to stay on .NET Framework for more couple of years, as Microsoft haven't dropped .NET Framework (even create a 4.8.1 version). For the next GTA installment, Grand Theft Auto VI, there are still every chance that script hooks will still use .NET Framework, instead of modern solutions, for reusing old code.

[^1]: This link goes to the RPH Discord. RAGE Plugin Hook original author, MulleDK is "away without official leave" for as least a year now and the latest available RPH on that website is still for game build 3095.
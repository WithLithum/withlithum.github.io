---
title: "Ticks and Loops in GTA V Scripting"
description: Telling you how everything really works.
date: 2023-12-11T08:23:14+08:00
categories:
    - Programming
tags:
    - gtav
    - scripthookv
    - dotnet
    - scripthookvdotnet
---

Most GTA V scripting newbies, when trying to do something repeatedly, would try to do this: (using C# as an example)

```csharp
while (condition)
{
    // do processing logic.
}
```

However, when they go and run this, they'll see that the game either freezes or crashes, with no clue of what went wrong.

## So what really went wrong?

In order to answer this question, I will have to explain how the GTA V scripts run.

All calls to the game and everything that deals with the game world needs to be synchronised. This means only one party (that is, either the game itself, a `ysc` script which is a type of script embedded in the game, or one of the custom scripts/game fibre provided by SHV, SHVDN, RPH, etc.) may deal with game functions at the same time, and script hooks will have to ensure that too.

The game achieves this using ticks. Each tick, the game runs once, and all other scripts run once. When they return or finish running one loop, they will have to explicitly tell the game by _yielding_ themselves so that the game engine can pass on executing the next stuff.

If one fails to yield in loop, the loop will run in a single tick, and the game freezes until the loop returns (and if the loop is a `while true` with no `break` that means never). Thankfully, ScriptHookV .NET and RAGE Plugin Hook both have safety measures in place to terminate scripts/plugins that fails to yield to other scripts for a set amount of time (this is usually 10 seconds but LSPD:FR players tend set it to one minute due to how long LSPD:FR takes to load).

[This RPH documentation article](https://docs.ragepluginhook.net/html/4c04eafe-2474-4f41-9087-ac47bb16d491.htm) explains this in detail.

## How do I fix this problem?

In order to allow the game and others to run, you will have to yield in your loops. How you do the actual yielding depends on the framework you are using.

### ScriptHookV

If you use ScriptHookV, you should do so whenever you need to do loops. Do this when looping:

```c
while (condition)
{
    WAIT(0);
    // ... your code
}
```

`WAIT(0)` here means that the script while pause and allow others to run, and resume at the next tick.

### ScriptHookV .NET, and FiveM .NET

You should try to avoid doing your own loops because the `Script` base class already providing a ticking function.

```csharp
public MyScript()
{
    Tick += OnTick;
}

private void OnTick(object sender, EventArgs args)
{
    // Your code.
}
```

ScriptHookV .NET will automatically do the yielding for you after your `OnTick` handler returns. But beware, if the `OnTick` function does not return, it will not yield too.

You can manually yield by calling `Script.Yield()` if necessary.

### RAGE Plugin Hook

Yielding in RPH is more or less the same as of in ScriptHookV.

```csharp
public static void Main()
{
    while (condition)
    {
        GameFiber.Yield();
        // Your code.
    }
}
```

### Others

Other script engines (FiveM Lua, Lua ScriptHook) should be similar to ScriptHookV or the ScriptHookV .NET, in terms of yielding.

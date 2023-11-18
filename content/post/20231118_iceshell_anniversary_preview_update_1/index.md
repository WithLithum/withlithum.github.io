---
title: "IceShell Anniversary Preview - Status Update 1"
description: New command resolution system and more!
date: 2023-11-18T08:55:23+08:00
image: cover.png
categories:
    - Programming
tags:
    - iceshell
    - dotnet
    - shell
    - command-line
---

This is the first status update for the IceShell Anniversary Preview (v0.3.0-alpha), which is set to release at 1st of Feburary 2024, exactly one year after its first commit. While I've been working on it for the last year, there weren't some major improvements until the Anniversary Preview were planned.

The Anniversary Preview comes with the overhual of one of the systems that IceShell relies on - internal command registry.

## Internal Command Registry

Let's outline the main changes made to the Internal Command Registry system:

### Rewrite of registry system

- The attribute-based command system have been decoupled from the command registry system. Instead, you need to maintain an instance of `AnnotatedCommandRegistry`. This change does not bring breaking API changes to the attributes surface, though!
- You can now register commands with a `CommandRegistryEntry` which describes the command, and a `CommandDelegate` which handles the execution of the command.
- The command manager (`ICommandManager`) and dispatcher (`ICommandDispatcher`) interface have been merged to a single interface, the `IRegistryCommandDispatcher` interface!

Here is an example of how to register an annotated command under the new system:

```csharp
var registry = new AnnotatedCommandRegistry(dispatcher);
registry.Register(typeof(AnnotatedCommand));

// Whereas the shell is the dispatcher that is passed via shell or module initialisation.
// and AnnotatedCommand is the command you are going to register.
```

Here is a demonstration of how to register a command without an annotated shell command class:

```csharp
var entry = new CommandRegistryEntry("command");
var val = entry.AddValue("value", 0);

dispatcher.Register(entry, (shell, executor, context) => {
    Console.WriteLine(context.Arguments.Values[val]);
});

dispatcher.Execute("command testvalue"); // testvalue
```

### Separation of command aliases and IDs

Once a time ago, there are no command aliases, and commands are directly referred to with their ID (called _Name_ at that time). Because of there are requirements for supporting commands with multiple names, an alias feature was added. The alias feature were implemented by registering the same command twice or more times under different names.

In the Anniversary Preview, the command IDs and aliases are now registered separately:

- The same command registered with two or more IDs are treated with separate commands (or separate instances of the same command, if you thinks so).

- Multiple aliases will correspond to unique IDs of commands, and the command dispatcher will use the ID to acquire the command definition from the registry.

- Command IDs are case sensitive, while command aliases are case insensitive.

Let's suppose there is a command registered with ID `test`. By default, the ID of the command is automatically registered as an alias of the command. Here is an example demonstrating the differences between aliases and IDs:

```csharp
dispatcher.Register(entry, routine);
// Entry is a command with ID "test".

// Command IDs are case sensitive.
Console.WriteLine(dispatcher.AnyCommand("test")); // True
Console.WriteLine(dispatcher.AnyCommand("TEST")); // False
Console.WriteLine(dispatcher.AnyCommand("tEsT")); // False

// Command aliases are case insensitive.
Console.WriteLine(dispatcher.HasAlias("TEST")); // True
Console.WriteLine(dispatcher.HasAlias("test")); // True
Console.WriteLine(dispatcher.HasAlias("TeSt")); // True
```

## Platform Modules

The Anniversary Preview update introduces two Platform Modules, the GNU/Linux platform module and Windows platform module.

The two modules are bundled in a standard release package and is automatically enabled on their respective platforms.

## Command Changes

### Bundled Commands

- Added the new `CHMOD` command in the GNU/Linux platform module

- Added the new `DATE` command for viewing date and time

- Added the new `FIND` command for searching for text strings in pipeline input

- Modified the syntax of the `PROMPT` command and the prompt itself to support more placeholders

- Added the new `MEM` and `VOL`commands in the Windows platform module

- Added the new `MORE` command to separate large chunk of text to pages with size determined by the size of the text buffer area of your terminal (emulator) window or screen.

### Command Help

The description of commands and its parameters are no longer hardcoded. Instead, the description will be shown as a localised message with automatically generated message ID.

The message ID format is shown as below:

| Type                         | Message ID Format                                           |
| ---------------------------- | ----------------------------------------------------------- |
| Command description          | `command.<command-name>.description`                        |
| Value parameter description  | `command.<command-name>.value_<reference-name>.description` |
| Option parameter description | `command.<command-name>.option_<identifier>.description`    |

### Command Actions

The support for a single ampersand to connect two commands have been added. A single ampersand means to execute the command to the right regardless of the result of the command to the left.

### Argument Parsing

The support for multiple integral types have been added to annotated commands. Currently this includes `Int32`, `Int64`, `Byte`, `SByte`, `Byte` and also `Enum`.

I plan to add this argument conversion service to non-annotated commands.

## In the end...

I would like to thank [JetBrains](https://jetbrains.com/) for providing a free licence for their tools to help develop IceShell. Their tools have really helped me to make the later than much more major and important changes to the core systems. Without their tools, I would have costed much more time to may these important changes.

That's all for now! I will continue to add new features and updates to the Anniversary Preview and improve it further in the meanwhile.

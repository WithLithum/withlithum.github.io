---
title: C♯ Extension ".NET SDK Install" Workaround
description: How to add .NET installed to the .NET SDK Install extension
date: 2023-08-04T19:31:01+08:00
categories:
    - Programming
image: cover.png
tags:
    - dotnet
    - csharp
    - vscode
---

> **Note on the title**
>
> Hugo does not let me to include hashtag '`#`' because it is a special character and Hugo did not try to
> escape it. So I used ♯ as an alternative.
>

C# extension 2.0.320 introduced a Language Server as the default option of language service instead of the good ol' OmniSharp. This however, presumably added dependency to .NET SDK Install extension. The Extension, unfortunately, at the time of when this post is written, _doesn't care about any of the .NET SDK installed in your computer_, and will try to download SDK if it was not previously downloaded by this SDK Install extension _even if appropriate SDKs are installed in your computer, AND is in the PATH_.

This issue is being tracked via issue [#135](https://github.com/dotnet/vscode-dotnet-runtime/issues/135), and is being worked on via PR [#1081](https://github.com/dotnet/vscode-dotnet-runtime/pull/1081).

## Workaround

In your **User** settings, find setting "Dotnet Acquisition Extension: Existing Dotnet Path" (`dotnetAcquisitionExtension.existingDotnetPath`), click `Edit in settings.json`, and add lines between braces according to your Operating System and method used to install it:

### APT (Ubuntu repository)

This may or may not work with Microsoft's repository version.

```json
{
    "extensionId": "ms-dotnettools.csharp",
    "path": "/lib/dotnet/dotnet"
}
```

### Windows (via SDK Installer)[^1]

```json
{
    "extensionId": "ms-dotnettools.csharp", 
    "path": "C:\\Program Files\\dotnet\\dotnet.exe"
    // Path to "dotnet" command executable file, not to your SDK
}
```

### Mac[^1]

```json
{
    "extensionId": "ms-dotnettools.csharp",
    "path": "/usr/local/share/dotnet/dotnet"
}
```

### Others

Replace `<Path to 'dotnet' executable not directory>` with the path to your `dotnet` executable (also called "command line driver", usually `(dotnet directory)/dotnet` or `(dotnet directory)/dotnet.exe`).

```json
{
    "extensionId": "ms-dotnettools.csharp",
    "path": "<Path to 'dotnet' executable not directory>"
}
```

[^1]: Referenced <https://github.com/dotnet/vscode-dotnet-runtime/blob/main/Documentation/troubleshooting-runtime.md#manually-installing-net>, only changed extension ID

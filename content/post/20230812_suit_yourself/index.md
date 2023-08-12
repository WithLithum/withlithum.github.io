---
title: "The so-called SponsorLink and how to replace projects with it"
description: About the damned "SponsorLink"
date: 2023-08-12T09:44:23+08:00
image: cover.png
categories:
    - Programming
tags:
    - dotnet
    - sponsorlink
    - sponsors
    - evaluation
---

SponsorLink. That stuff they introduced in a mocking library. Now I am too lazy to give an introduction to a product I 100000000% precent dislike so let's skip introduction.

## What the hell is SponsorLink anyways?

SponsorLink is a piece of _obfuscated_[^1] middleware that runs in the building process that connects to the their own services with the hash of your email address (collected from your Git configuration in the background _without any hints_ nor your explicit consent)[^2] to check the repository _on GitHub_. The makers of SponsorLink claims that they do not collect any more information than that. Currently this software does not impact end users of a software, only the build process.

If SponsorLink finds that you have not yet paid the author on GitHub Sponsors it will do something like adding delays in your build and raising warnings (which could prevent build) in your project.

[Issues](https://github.com/devlooped/SponsorLink/issues/33) were raised about the behaviour but they are just ignored by the original author.

## Why does Devlooped create it in the first place?

**MONEY. WHO DOESN'T LIKE MONEY?**

## Can I harm my project if I have added it?

Yes, in all ways. The build delay and required app install discourages people from sponsoring your project, and support a fork that doesn't have these features or support an entirely different project instead.

New harmful feature such as a dialog stating that you did not sponsor the dependency, injected into your end product[^3] can be added at any time, and Devlooped may decide if this also affects projects in CI.

## Replacements for SponsorLink-ed projects

You can also try to fork and wipe the project clean of SponsorLink, but beware of hidden caveats.

- Moq: Use `NSubstitute`. See AlbertoMonteiro's [moq-to-nsubstitute](https://github.com/AlbertoMonteiro/moq-to-nsubstitute), and you may try Creastoff's [Moqstitute](https://github.com/Creastoff/Moqstitute).

## I can't replace these projects!

Disconnnect all connections to Internet if you work on any project that is SponsorLink-ed, but I do not gurrantee this will work forever. Devlooped can simply add a feature to require Internet connection when building project.

## Issues with this Article

Use the [Issues](https://github.com/WithLithum/withlithum.github.io/issues) section.

[^1]: [Message in C# discord](https://discord.com/channels/143867839282020352/143867839282020352/1139212669816156300)
[^2]: Hashing is not something you would debate, rather the _consent_ is. Even not a email (hashed or not), random ID would suffice identification of a single user/machine. In other words, email reveal is not the primary concern of the project.
[^3]: This essentially makes your users think that you made your software based on evaluation or pirated parts.

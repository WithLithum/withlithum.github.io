---
title: "The so-called SponsorLink and how to replace projects with it"
description: About the "SponsorLink" and its effects
date: 2023-08-12T09:44:23+08:00
image: cover.png
license: CC-BY-ND-4.0
categories:
    - Programming
tags:
    - dotnet
    - sponsorlink
    - sponsors
    - moq
    - nugetizer
    - activation
    - evaluation
---

_This article was edited since (at 11th of November, 2023) its initial release to reflect the current changes._

Parts of this article is based on my personal assumptions and knowledge. Yet, this does not disqualify the initial purpose of this article. I solely will decide whether a problem is material to the cause of this article or is totally immaterial to the matter.

**Disclaimer: I am not responsible for any damages, loss or costs as induced by or otherwise related to or associated with anything in the article.**

## Questions and Answers

### What is SponsorLink anyways?

SponsorLink is a piece of _formerly obfuscated_[^1]MSBuild middleware that runs in the building process that connects with a remote server with the hash of your email address (the consent requirement was not there until recently) in order to verify you have sponsored the project. If you have not sponsored the project, a build delay and a build warning will be added for the first time that project was built on the same IDE instance. (It was added for each build before it was changed to the current system persumably due to critics). The makers of SponsorLink claims that they do not collect any more information than that. Currently this software does not impact end users of a software, only the build process.

Moq have since removed it claiming it "breaks builds on some systems" rather than other valid concerns, persumably with intention to bring it back at a later time. (Further research found out that Synk had marked Moq as having unwanted behaviour.) The version number that was released to remove SponsorLink was `4.20.69`, and I personally would (because of the number `69`) understand it as a some sort of despise to those who are against the SponsorLink.

[Issues](https://github.com/devlooped/SponsorLink/issues/33) were raised about the behaviour but they are just ignored by the original author. The original author does does have a discussion thread but his response to critics were nothing more than just pickling on some very minor immaterial details in order to (by attempting to try to convince other people that the critics are wrong) provide a somewhat justifiable cause for such malicious middleware.

### Why is it not acceptable?

The designed purpose of the SponsorLink is to _take something that users already have and expect_ (build project without delays and unsolicited information) _make it exclusive to those who have paid for_. SponsorLink is mainly used in projects that are licensed under a permissive license, and kzu persumably made this middleware based on the same intention that made Marak Squires made `color.js` and `faker.js` private (before community forked these). kzu may have wanted to make it similar to a product activation system but refrained from parts of the idea because of critics, and based on his response to critics in his own comment section, persumably in order to "fight back critics and prove I am totally correct".

There are tons of other acceptable ways for open source sustainability, for example, the MkDocs Material sponsorware model is one of sponsorship models that I personally does not disagree with.

At least personally to me, begging for sponsorship in an annoying way while it is not strictly necessary means that people would simply get annoyed and stop using the project, not even to say if they are willing to pay for that or not. Everybody can define what is considered as "annoying" for themselves, yet generally a sponsorship link in the README section and in package description, as well as a one-time, non-modal *and* non-interruptive notice after package install is generally considered OK.

### What are the Replacements for SponsorLink-ed projects?

You can also try to fork and wipe the project clean of SponsorLink, but beware of hidden caveats.

- Moq: Use `NSubstitute`. See AlbertoMonteiro's [moq-to-nsubstitute](https://github.com/AlbertoMonteiro/moq-to-nsubstitute), and you may try Creastoff's [Moqstitute](https://github.com/Creastoff/Moqstitute). I have also [created a fork of Moq](https://codeberg.org/NexusKrop/moq-build) in order to strip all SponsorLink whenever and wherever it appears in the project. It should pass all tests and be 100% compatible with your current code.
- NuGetizer: Remove the package and all packages as related to it. You do not need it to build your software.

#### But I cannot replace these packages!

Disconnnect all connections to Internet if you work on any project that is SponsorLink-ed, but I do not gurrantee this will work forever. Devlooped can simply add a feature to require Internet connection when building project.

You may also fork the project.

## Issues with this Article

Use the [Issues](https://github.com/WithLithum/withlithum.github.io/issues) section.

[^1]: [Message in C# discord](https://discord.com/channels/143867839282020352/143867839282020352/1139212669816156300)
[^2]: Hashing is not something you would debate, rather the _consent_ is. Even not a email (hashed or not), random ID would suffice identification of a single user/machine. In other words, email reveal is not the primary concern here.
[^3]: This essentially makes your users think that you made your software based on evaluation or pirated parts.

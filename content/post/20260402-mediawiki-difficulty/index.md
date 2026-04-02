---
title: "The Difficulty of Maintaining a MediaWiki website"
description: The lack of default.
date: 2026-04-02T17:31:10+08:00
build:
    list: always    # Change to "never" to hide the page from the list
---

If you do not know, I maintain [The Lily Wiki](http://lilymc.miraheze.org/wiki/), the "official" wiki for _Lily - Connections Lost_ and sequels. So I may have a fair say on how hard it is to properly set up a new wiki.

This article is my personal opinion on the difficulties, challenges and problems of setting up and running a MediaWiki powered website, and does not attempt to cover everything. 

## Setting up templates

The default loadout of vanilla MediaWiki is **nothing** more than just one single a start page made out of very simple markup.

Most MediaWiki powered wikis will want their wiki to be aesthetically similar to Wikipedia - this means they are to have infoboxes, message boxes, hat notes, etc. Some wiki farms, such as Fandom or wiki.gg, does provide a usable minimum "default loadout"[^1][^2] that you can work with.

However - vanilla MediaWiki, and vanilla-ish farms such as Miraheze, comes with none of that. WMF does not provide a package for these essential templates. To have those commonly used templates, they must be copied off another wiki.

Wikipedia and other Wikimedia projects _are_ common sources of these templates[^3]. But, the catch is, they are:

- [not designed for external consumption](https://meta.miraheze.org/wiki/User:Raidarr/Don%27t_import_Wikipedia)
- have many dependencies for local DRY purposes that makes it harder to import and maintain
- can be more than decades old and did not always follow modern web practices (e.g. uses `<table>` where flex-boxes or grids should have been used instead)
- not licensed under a code licence but instead are licensed under the project content licence which can be very restrictive

Miraheze, Fandom and other major template sources imports directly from Wikipedia and tries to process as-is to get rid of unnecessary dependencies. However, even with that, you will usually end up with a big pile of mess that is ugly to maintain and not up to date with web standards.

### Infoboxes

You generally have two choices when trying to implement infoboxes:

- Using pre-established "solutions" such as DRUID and Portable Infobox
- Copying Wikipedia infoboxes

Portable Infobox is an extension created by Fandom (formerly Wikia). However, it uses a special renderer and breaks page previews. The version of the extension that you can download and install is ripped from old pre-UCP era Fandom code by a Miraheze maintainer. That version is not polished enough as the version currently on Fandom.

In the opposite end, the Wikipedia infobox template have not been redesigned for decades potentially, and is a pain to redesign on your end. I never got redesigns quite to work.

In the end, I chose DRUID. It is customisable, it works, have enough documentation on how to install on non-wiki.gg wikis and most importantly, do not break page reviews.

### Message boxes

The Wikimedia message box suite consists of a module, configuration files and a set of templates.

Unfortunately, the usually useful source, Miraheze Dev, completely messes up on providing this suite correctly. `Ambox` is supposed to be a variant of `Message box` module call but instead is a wiki text version of the `Mbox` template, which is `Ombox`.

So you are resorted to importing WMF projects, which means pulling in a lot of dependencies and potentially unneeded wiki-specific configuration such as localisation text.

## Skins

The default skin for MediaWiki is Vector 2010, which looks outdated and ugly. Its modern counterpart, Vector 2022, is set as default and needs configuration to enable and use.

I recommend you to stick with Vector 2022 unless you genuinely need to design your wiki to be different. Skins _do not style templates_, and templates more than often hardcodes colours, which breaks dark mode.

My favourite modern theme, Citizen, for example, makes most templates look out of place. If you care about visual consistency, like I do, you will have to make some deep customisation - because Citizen theme is _not_ designed for the usual WMF templates suite but are designed for the specialised templates on Star Citizen Wiki.

## "Getting it right"

It is very difficult to get a MediaWiki site up without either having to reinvent the well or leaving technical debt that will make modernisation difficult several years later.

As I have stated above, many templates found on the Internet are riddled with many legacy practices not in line with the current web guidelines or harm customisation: For example:

- `<table>` is used for aligning content instead of CSS `grid` or `flex`
- bakes styles straight into the template which causes huge customisation problems

## A bit of advice

Setting up a MediaWiki-powered wiki is not for the faint of heart. It is a significant task that requires you to have basic understanding of web administration and development.

Think again before you choose MediaWiki. If the aesthetics of Wikipedia is what you are after, the difficulties of using this wiki software may not provide you the benefits for the aesthetics it provides. However, if you are capable enough, MediaWiki is a strong and versatile tool to host a public-editable website.

[^1]: https://defaultloadout.wiki.gg/
[^2]: https://ucp-internal-test-default-loadout.fandom.com/
[^3]: https://www.mediawiki.org/wiki/Manual:Importing_Wikipedia_infoboxes_tutorial

# Discord Widget Setup Guide

A guide for creating custom Discord profile widgets using Widgets v2 — includes both a fully-automated console script and a detailed manual walkthrough.

Compiled and maintained by **youcef** ([xgg.2](https://discord.com/users/1159928250148077728)).

> [!IMPORTANT]
>
> **Discord has disabled creating widgets on newly made applications completely.** If your application was created after this change, none of the methods in this guide (script or manual) will work — Discord blocks widget creation at the source now, regardless of how correctly the steps are followed.
>
> See [Discord's support post](https://support.discord.com) for more information.
>
> Discord has stated they're working on an official, easier way to create widgets for your profile — no ETA has been given. This guide is kept here for reference and for anyone with an application created **before** the cutoff, where the widget may still function.

## Background

None of this was invented from scratch. Widgets v2 is an undocumented, unofficial Discord feature, so the information here was pieced together from multiple community sources — guides, gists, and Discord servers where people were reverse-engineering this independently — then tested by hand, step by step, on real accounts. Several things in the original sources turned out to be outdated, incomplete, or broke silently in practice; those gaps were found through actual trial and error and patched here, rather than assumed. This repo is the result of that process, kept up to date as Discord's behavior keeps shifting.

If you spot something that's since changed or broken, opening an issue or PR is welcome.

## Read the guide

| Language | File |
|---|---|
| العربية  | [ar.md](./ar.md) |
| English | [en.md](./en.md) |

More languages may be added over time. If you'd like to contribute a translation, open a pull request adding a new `<lang-code>.md` file and it'll be listed here.

## What's inside

- **Part 1 — Fast method:** a single console script that creates the application, enables the required permissions, designs a default widget, publishes it, adds it to your profile, and activates its identity automatically
- **Part 2 — Manual method:** a full step-by-step walkthrough for anyone who wants to understand or control every step themselves
- Troubleshooting sections for both methods, based on errors actually hit during testing — not hypothetical ones
- Helper scripts to list and remove widgets from your profile

## Related

- Companion tool for the identity activation step (no terminal required): [widget-tool](https://widget-tool.pages.dev)

## Disclaimer

This is unofficial, community-documented information based on reverse-engineered, undocumented Discord features. It is not affiliated with or endorsed by Discord. Behavior may change at any time without notice — as demonstrated by the deprecation above.

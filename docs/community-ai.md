# Community suite for Umbraco - AI moderation add-on

Optional AI-assisted moderation for the Digital Wonderlab community suite:
[**Community Forum**](https://www.nuget.org/packages/DigitalWonderlab.CommunityForum)
and [**Community Comments**](https://www.nuget.org/packages/DigitalWonderlab.CommunityComments).
One install, one toggle - AI review covers forum posts **and** page comments alike.

Install this add-on to layer an AI spam/toxicity review on top of the suite's
built-in moderation rules engine. It runs through the official **Umbraco.AI**
layer, so it is provider-agnostic - you bring your own connection and profile
(Anthropic/Claude is supported), and your keys and AI spend stay governed by
Umbraco.AI.

**Without this add-on, the suite still moderates** with its always-on
deterministic rules engine (spam, profanity, links, obfuscation, and your own
blocked/spam/promotional word lists). The add-on only adds the AI layer on top;
if the AI is unavailable, posting is never blocked - the rules verdict stands.

## Requirements

- `DigitalWonderlab.CommunityForum` and/or `DigitalWonderlab.CommunityComments`
- Umbraco CMS **17 LTS**, **.NET 10**
- `Umbraco.AI` with a configured connection + profile, plus a provider package
  (e.g. `Umbraco.AI.Anthropic`) - chosen and installed by you

## Install

```
dotnet add package DigitalWonderlab.CommunityAi
```

Then configure an Umbraco.AI connection/profile in the **AI** section, and switch
on AI moderation in **Community → Settings → Moderation**.

## Licence

Free to use. See LICENSE - proprietary, no redistribution or resale.

Part of the `DigitalWonderlab.*` family of Umbraco packages. Built by
[Digital Wonderlab](https://digitalwonderlab.com).

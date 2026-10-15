# Community AI Moderation for Umbraco

An optional add-on for
[Community Forum](https://www.nuget.org/packages/DigitalWonderlab.CommunityForum) and
[Community Comments](https://www.nuget.org/packages/DigitalWonderlab.CommunityComments). It
does nothing on its own: install it alongside one or both of those packages.

Both packages already check every post and comment with a rules engine. This add-on asks an
AI model to review each one as well, which catches things word lists miss, such as scams
written in plain, polite language. One setting turns it on for forum posts and comments
together.

It works through [Umbraco.AI](https://marketplace.umbraco.com/package/umbraco.ai), so you
choose the AI provider and use your own account and API key. We never see or store your key,
and the provider bills you directly.

If the AI service fails, isn't set up, sends back something unreadable or takes longer than
8 seconds, the rules engine's decision is used instead. Posting is never blocked by an AI
outage.

## Requirements

- `DigitalWonderlab.CommunityForum` and/or `DigitalWonderlab.CommunityComments`
- Umbraco CMS 17 on .NET 10
- `Umbraco.AI`, with a connection and profile set up, and a provider package such as
  `Umbraco.AI.Anthropic`

## Install

```
dotnet add package DigitalWonderlab.CommunityAi
```

Set up a connection and profile in the backoffice **AI** section, then switch on AI
moderation in **Community > Settings > Moderation**.

**Using Anthropic?** Create the API key inside a workspace. Anthropic rejects
organisation-level keys for this ("This API key is not scoped to a workspace"), and the
Umbraco.AI Anthropic provider has no way to name a workspace, so every check would fall back
to the rules engine.

## Problems and ideas

Report bugs or suggest features at
[github.com/dwlgit/community-suite/issues](https://github.com/dwlgit/community-suite/issues).

## Licence

Free to use on any number of sites, including sites you build for clients. You can keep the
package's files in your site's source control and hosting, and change the views and styles it
adds. Reselling or redistributing the package itself is not allowed. The full licence is in the
`LICENSE` file in the package.

Built by [Digital Wonderlab](https://digitalwonderlab.com).

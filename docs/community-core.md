# Community Core for Umbraco

Shared code for
[Community Forum](https://www.nuget.org/packages/DigitalWonderlab.CommunityForum) and
[Community Comments](https://www.nuget.org/packages/DigitalWonderlab.CommunityComments).
You don't need to install it yourself: it comes with either package.

## What it contains

- **The moderation rules engine**, which scores every post and comment for spam, profanity,
  links and disguised words, plus your own blocked words and phrases and a strictness level.
- **The hook for AI moderation**, used by the optional `DigitalWonderlab.CommunityAi`
  package. If the AI is unavailable, posting carries on with the rules engine's decision.
- **The Community section** in the backoffice, with one moderation queue for forum posts and
  page comments, and the shared settings.
- **Member sign-in**: sign in, sign out, forgotten password and reset, under
  `/community/auth/`, plus a plain `/community/sign-in` page that works without the forum.
  It includes email verification and limits on repeated attempts.
- **HTML cleaning** for everything members write.

## Configuration

| Setting | What it does |
| --- | --- |
| `Community:Auth:BaseUrl` | Your site's public address, used to build links in verification and password reset emails. |
| `Community:Auth:ResetPageUrl` | Where a password reset link takes the member. Defaults to the sign-in page. |

Reserved addresses: `/community/sign-in` and `/community/auth/*`.

## Licence

Free to use. Redistribution and resale are not allowed. See the `LICENSE` file in the package.

# Community Core for Umbraco

The shared core for the Digital Wonderlab community suite. You normally don't install this
directly - it arrives as a dependency of:

- **DigitalWonderlab.CommunityForum** - an Umbraco-native discussion forum.
- **DigitalWonderlab.CommunityComments** - WordPress-style page comments for any Umbraco page.

## What it provides

- **Moderation rules engine** - deterministic, always-on spam/abuse scoring (links, structure,
  spam lexicon, tiered profanity, plus admin-configured blocked words / spam / promotional
  phrases and a sensitivity level).
- **AI moderation seam** - the optional `DigitalWonderlab.CommunityAi` add-on layers an
  Umbraco.AI review on top; an AI outage never blocks posting.
- **Community backoffice section** - one shared home with the unified **moderation queue**
  (forum posts and page comments in one list) and suite-wide **Settings**.
- **Shared member authentication** - sign in, sign out, forgot password and reset, at stable
  routes under `/community/auth/`, plus a neutral, self-contained **`/community/sign-in`** page
  that works with no forum installed and round-trips a same-site `returnUrl`. Email
  verification, throttling on register/forgot/resend, and friendly handling of expired
  antiforgery tokens are built in.
- **Content sanitiser** - allow-list HTML sanitisation for all member-generated content.
- **Shared settings store** - section-level defaults; per-content settings override them.

## Configuration

| Setting | What it does |
| --- | --- |
| `Community:Auth:BaseUrl` | The base URL used to build emailed links (verification, password reset), so links never depend on a spoofable host header. |
| `Community:Auth:ResetPageUrl` | Where a password-reset link lands. Defaults to the built-in sign-in page. |

Reserved front-end routes: `/community/sign-in` and `/community/auth/*`.

Licence: proprietary, free to use - see LICENSE.

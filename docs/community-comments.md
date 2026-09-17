# Community Comments for Umbraco

WordPress-style comments for any Umbraco page, moderated by the same engine and from the
same queue as Community Forum.

Members comment on your pages; every comment runs through an always-on deterministic
moderation rules engine (spam, abuse, link-flooding, blocked words), with optional
AI-assisted review that integrates with the official **Umbraco.AI**. The shared
**Community** backoffice section - installed
automatically - gives you a moderation queue and suite-wide settings out of the box.

## Goes great with Community Forum

Comments is part of the same suite as
[**Community Forum for Umbraco**](https://www.nuget.org/packages/DigitalWonderlab.CommunityForum),
an Umbraco-native, SEO-first discussion forum. Install both and forum posts and page
comments share one moderation engine, **one moderation queue**, one settings screen and one
optional AI add-on, with the same members and the same Moderators group.

## Add comments to a page - two ways

1. **No code:** drop the **Comments (block)** into any Block List / Block Grid area.
   Placing the block *is* the opt-in, so there is nothing else to switch on.
2. **Template:** add the **Commentable** composition to a document type, tick *Enable
   comments* on the page, and render `@await Component.InvokeAsync("DwlComments")` in
   the template.

> **If comments do not appear on a page using route 2**, the page's *Enable comments*
> toggle is off, or the document type is missing the **Commentable** composition (so the
> toggle never appears and reads as off). The component renders nothing in that case,
> by design. To render comments on every page using a template regardless of the toggle,
> invoke it with `@await Component.InvokeAsync("DwlComments", new { ignoreToggle = true })`.

## Features

- Members-only commenting with one level of replies
- Comment composer with emoji, plus **edit and delete your own comment**
- Member avatars on every comment (Gravatar, with a neutral placeholder fallback)
- Action feedback as unobtrusive toasts, with inline validation instead of a full page reload
- Always-on moderation rules engine + admin-configured blocked words / spam / promotional phrases
- Optional AI moderation (Umbraco.AI) - shared with Community Forum, one toggle for both.
  If it errors or does not answer within 8 seconds, the rules verdict stands
- Member **Report** action, one open report per member per comment and rate limited;
  enough distinct reports auto-hide a comment for review
- Inline moderator actions (Approve / Remove) for members in the Moderators group
- Unified backoffice moderation queue + settings in the Community section
- **Hold every comment** for review per page, or let the rules engine decide (the default)
- **Account erasure**: when a member deletes their account through Community Forum, their
  comments stay readable but are stripped of anything identifying and shown as
  "[deleted user]". On a site without the forum, call `ICommunityErasureService` from your
  own delete-account flow to get the same result
- Self-contained, brand-neutral styling that picks up your site's `--primary` / `--accent`
- Honeypot + rate limiting, allow-list HTML sanitisation, CSRF-protected forms

## Configuration

The "sign in to comment" prompt links to the built-in `/community/sign-in` page by
default. Point it at your own login page in `appsettings.json`:

```json
{
  "Community": {
    "Comments": {
      "LoginUrl": "/login"
    }
  }
}
```

A `returnUrl` query parameter is appended so members come back to the page they were
commenting on. You can also override it per invocation via the `DwlComments` component.

## Limitations

- **Single-culture.** A comment is stored without a culture, so every language variant of
  a page shares one conversation.
- **Moderated inline or in the backoffice.** Comments do not appear in the forum's
  front-end moderation queue; members of the Moderators group action them on the page
  itself, and the backoffice queue covers comments and forum posts together.
- **Commenting requires a signed-in member, not a verified one.** The forum's
  email-verification gate does not apply to comments.
- **No registration flow of its own.** On a site without Community Forum you need a way
  for members to register; the neutral `/community/sign-in` page handles sign-in and
  password reset only.
- **A page's comments are loaded in full**, not paged.

Umbraco 17 LTS. Free to use - see LICENSE.

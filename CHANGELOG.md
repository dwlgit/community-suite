# Changelog

## 1.2.2 - Front-end moderation queue uses the community layout

- The front-end moderation queue is now rendered inside the full community layout (masthead, brand,
  navigation) instead of a bare standalone page, so moderators stay in the community and can keep
  navigating. It is served by the forum via a content finder at `{forum}/moderation`, mirroring the
  `/account/` and `/member/` pages.
- The queue shows forum items only. Front-end moderators (members in the Moderators group, no
  backoffice account) handle the forum; comment moderation stays in the backoffice queue.
- The shared resolve endpoint returns you to the page you actioned from (works behind any forum domain).
- Removed the standalone Core moderation page (superseded by the forum-rendered page); comments-only
  installs moderate comments from the backoffice.

## 1.2.1 - usability fixes from hands-on testing

Accounts and composer:
- Show/hide toggle on password fields (register, sign-in, reset) so you can see what you type.
- Images pasted into a post are clamped to the post width and a sensible max height instead of
  rendering at full size.

Threads and tags:
- The thread author (or a moderator) now gets a clear "Add tags / Edit tags" link by the tag row;
  it opens the first-post editor where tags are set.
- Spacing added between the tag row and a poll.

Report and moderation:
- The Report button now returns you to the thread with a confirmation toast (it was bouncing to the
  board listing, which read as "nothing happened").
- The front-end moderation queue has a community top bar with a "Back to community" link so
  moderators are not stranded on a standalone page.
- Queue actions relabelled "Remove post" / "Dismiss report" with a short explanation of the difference.

Avatars:
- A new member's held first post now shows their avatar (it was blank).
- A deleted user shows a neutral avatar placeholder instead of a "[" bracket.

## 1.2.0 - QA batch: feedback, moderation, comments parity, front-end mod queue, GDPR delete

Feedback and composer:
- Action feedback now shows as toasts (forum and the embedded Comments component) instead of a
  top-of-page banner that was easy to miss after a reload.
- The composer validates inline (empty body / missing title) and highlights the field instead of a
  full-page reload that jumped to the top.
- Liking and subscribing update in place without reloading the page.

Moderation and members:
- A front-end moderation queue at /community/moderation lets members in the Moderators group
  approve/remove/dismiss held forum posts and comments without a backoffice account.
- Private messages are no longer run through the public spam filter (they were being silently dropped).
- The "new member" gate is time-based, so posting rapidly can't bypass first-post moderation.
- Authors can see their own held reply (awaiting review) instead of it seeming to vanish.
- Banned/muted members are marked; deleting a post now asks for confirmation.

Accounts:
- "Delete my account" (GDPR): your posts stay but are permanently anonymised, and your personal data
  is removed. You can no longer be sent messages once deleted.
- Account page shows your recent activity; wider signature box.

Board list, threads, search:
- Author avatars, a Solved badge, and a bell on threads you're subscribed to; "Start a thread" works
  from inside a thread; pinned/locked pills no longer indent the title; search shows the board;
  editing your opening post lets you re-define the thread's tags.

Comments component:
- Moderator buttons match the forum; author avatars; an emoji picker; edit/delete your own comments.

## 1.1.2 - held-thread row is not a dead link

- A held thread shown to its author in the board list is no longer a link (its public page
  doesn't exist yet, so clicking 404'd). It renders faded and non-clickable until approved.

## 1.1.1 - moderation visibility

- Authors now see their own thread that's held for moderation in the board list, greyed out
  with a "Pending review" flag, so it doesn't appear to vanish after posting. It stays hidden
  from everyone else until a moderator approves it.

## 1.1.0 - security and correctness fixes ahead of public launch

- Email verification now returns the member to the forum (where the "you're verified"
  confirmation shows) instead of dropping them on the host site's homepage.
- Verification, password-reset, "you already have an account" and reply-notification
  emails now use a branded HTML shell (header, heading, primary button, copy/paste
  fallback link, footer) instead of a bare paragraph.
- Signing in returns the visitor to where they set out from: the sign-in form carries a
  same-site `returnUrl` (e.g. the article they were signing in to comment on) and honours
  it after login, rejecting off-site URLs.
- Authors are notified in-app when a post held for moderation is approved and goes live.
- The dev-only password-reset and verification link fallbacks (shown when no SMTP is
  configured) are now gated to the Development environment.
- Members-only boards are excluded from search results, RSS feeds, the XML sitemap,
  the recent-activity sidebar, tag pages and public profile listings.
- Backoffice management APIs now require access to the Community section (the installer
  requires an admin), not just any backoffice login.
- Moderation now runs on edited posts and direct messages as well as new posts; direct
  messages also gained their own flood guard and recipient validation.
- Registration no longer reveals whether an email already has an account, and register,
  forgot-password and resend-verification are rate limited per IP.
- All front-end form POSTs (register, profile, forum posting/replies, comments) now degrade
  gracefully on a stale antiforgery token - a "session expired, please try again" redirect
  instead of a raw HTTP 400 - so an output-cached form, a rotated key or a first-visit
  missing cookie no longer dead-ends the user. Previously only the shared sign-in endpoints
  were graceful; registration in particular could 400 outright.
- New `Community:Auth:BaseUrl` setting: emailed links (reset, verification, reply
  notifications) can be built from a canonical origin instead of the request host.
- Reply notification emails are sent from a background task instead of holding the
  poster's request open, and reply/post counters update atomically under concurrency.
- Thread creation runs in a single transaction, titles are clamped to the column length,
  and a unique subscription index prevents duplicate reply emails.
- Report abuse hardening: one report per member per post, and self-reports are ignored.
- Gravatar URLs use SHA-256 email hashes (MD5 is dictionary-reversible), and comment IP
  hashes are keyed with the site id.
- Author profile links, mention links and message redirects are built from the forum's
  own URL so they work behind a relative domain; stored notification links likewise.
- Comments: reply parents from a different page are rejected.
- Assorted hardening: backoffice manifests no longer allow public access, installer
  report output is HTML-escaped, and queue/settings dashboards surface API failures
  instead of reporting success.
- Docs: added an **Umbraco Cloud** install section documenting the `cloud.gitignore`
  exclusions required for the package's buildTransitive-delivered `App_Plugins`/`Views`,
  so Cloud CI/CD sync doesn't jam on package-owned files. (No code change - purely a
  consuming-site configuration step.)

## 1.0.6 - relative-domain link fixes

Sweep of hardcoded root-relative links (and dead-end sign-in prompts) that 404'd when
the forum ran behind a relative domain.

## 1.0.5 - shared authentication in Core

Authentication moved into the shared Core package with a neutral `/community/sign-in`
page (works on comments-only sites) and friendlier antiforgery-expiry handling.

## 1.0.4 - configurable Comments sign-in link

The "sign in to comment" prompt links to a host-configured URL
(`Community:Comments:LoginUrl`) instead of assuming a forum is installed.

## 1.0.3 - domain-aware content finders

Forum content finders are now domain-aware; forum URLs 404'd when the forum ran behind
a relative domain.

## 1.0.2 - search box and icon fixes

Fixed the search box CSS overlap and the forum document type icon.

## 1.0.1 - installer fix

The guided installer failed on real installs with MasterTemplateNotFound; it now creates
the `_CommunityLayout` master template before the document-type templates.

## 1.0.0 - first release

The Digital Wonderlab community suite for Umbraco 17 LTS, published as four packages:

- **DigitalWonderlab.CommunityForum** - an Umbraco-native discussion forum. Boards are
  first-class Umbraco content; threads, posts, polls, reactions, tags, subscriptions, private
  messages and profiles live in the package's own transactional tables. Server-rendered and
  SEO-ready (clean URLs, structured data, RSS, XML sitemap), with a guided installer, full-text
  search, two-colour branding and dark mode.
- **DigitalWonderlab.CommunityComments** - WordPress-style comments for any Umbraco page.
  Members-only with one level of replies; added per page via the Commentable composition +
  `@await Component.InvokeAsync("DwlComments")`, or with zero code by dropping the **Comments
  block** into a Block List / Block Grid.
- **DigitalWonderlab.CommunityCore** - the shared core (installed automatically): the always-on
  moderation rules engine, content sanitiser, settings store, and the **Community** backoffice
  section with a unified moderation queue and suite-wide settings.
- **DigitalWonderlab.CommunityAi** - optional AI-assisted moderation via Umbraco.AI,
  layered on top of the rules engine for both forum posts and page comments. An AI outage never
  blocks posting.

Moderation highlights: deterministic scoring (links, structure, spam lexicon, tiered profanity),
admin-configured blocked words / spam / promotional phrases and sensitivity, member reporting
with auto-hide, inline front-end moderator actions, honeypot + rate limiting, allow-list HTML
sanitisation and CSRF-protected forms.

Everything is free to use - see LICENSE.

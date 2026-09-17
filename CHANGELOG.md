# Changelog

## 1.3.1 - pre-release audit fixes

A fresh review before public listing found a set of correctness, privacy and packaging problems.
This release fixes all of them. Nothing here changes how the packages are used, but two items
change behaviour on a misconfigured production site: read **Email links** below.

Moderation:
- **A moderation action is now ignored if the item has already been resolved.** Actioning one report
  closes every open report on that post, so a moderator working from a queue page that had been open
  for a while could replay an action and, for example, republish a post that was removed afterwards.
  Repeat actions on a resolved report or a comment that is no longer in the queue are now no-ops.
- **A rejected edit can no longer retag a thread.** Tags were written before the moderation verdict,
  so an edit that was blocked as spam still applied its tags. Tags are now written only when the edit
  is accepted or staged for review.
- **Comment reports are deduplicated, rate limited and clamped.** One open report per member per
  comment, at most five reports a minute per member, reports only against visible comments, and the
  reason is clamped to the column width (a long reason previously risked a truncation error on SQL
  Server). Forum reporting already did all of this.

Direct messages:
- **Banned and muted members can no longer send direct messages.** DMs were the one posting route
  that ignored profile status.
- The docs previously implied DMs were moderated and reportable. They are not: DMs are private and
  deliberately skip the public rules engine, and there is no recipient-side block or report in v1.
  The package README, docs and release notes now say so.

Privacy and accounts:
- **Account deletion now covers every installed package.** Deleting an account erased the member's
  forum data only, so on a site running page comments their comments stayed attributed to them. A new
  shared erasure service runs every installed package's erasure source in one pass. Comments are kept
  readable but stripped of identifying data and shown as "[deleted user]", matching the forum.
- A site running page comments **without** the forum can call `ICommunityErasureService` from its own
  delete-account flow to get the same result.
- **The stored comment IP hash is no longer written.** Nothing ever read it, and a hash of an address
  space small enough to enumerate is not anonymisation. The column stays (no migration needed) and any
  existing values are cleared when a member is erased.

Email links:
- **Password reset and email verification links are no longer built from an unvalidated `Host`
  header.** On a host that accepts arbitrary Host values, an attacker could previously have a victim
  emailed a working reset link pointing at a host they control. These emails are now sent only when the
  origin can be trusted: `Community:Auth:BaseUrl` is set, or ASP.NET Core `AllowedHosts` is a real
  allow-list, or the site is running in Development.
- **Action required on production if you set neither.** Password reset and verification will not send,
  and the reason is logged. Set `Community:Auth:BaseUrl` to your public site URL.
- Reply notification emails carry no token and still fall back to the request host.

Forum placement:
- **Members-only board titles are kept out of public listings even when the Forum node is not at the
  content root.** The lookup that identifies gated boards only searched content roots, so a nested
  Forum produced an empty list and private thread titles could surface in public recent activity,
  search and tag listings.
- **The installer adopts an existing Forum wherever it sits** instead of creating a second one at the
  root, and warns in the log when the Forum is nested.
- To be explicit, and now documented as such: **v1 supports one Forum, at the root of the content
  tree.** Its virtual pages (`/search`, `/account`, `/tag`, `/member`, `/messages`, `/notifications`,
  `/moderation`) resolve from a root Forum and will not resolve from a nested one.

AI moderation:
- **AI moderation calls now time out after 8 seconds** and fall back to the deterministic rules
  verdict. There was no timeout, so a stalled provider could hold a member's submit request open
  indefinitely. "Posting is never blocked if the AI is unavailable" is now accurate rather than
  aspirational, and the docs state the timeout.
- The add-on works with Community Forum, Community Comments, or both. The docs previously read as
  though it needed the forum.

Packaging and documentation:
- **`PackageProjectUrl` now points at the public project site**
  (https://dwlgit.github.io/community-suite/), which serves the Umbraco Marketplace metadata for each
  package. It previously pointed at the private source repository, so the Marketplace had no public
  fetch path for the manifests and anyone following the link from NuGet hit a private repository.
- `RepositoryUrl` has been removed rather than repointed: the source is not published, and pointing it
  at the documentation site would imply otherwise.
- **NuGet release notes were still describing 1.1.0** on the Forum and Comments packages, including a
  claim that direct messages run through moderation. Rewritten for this release.
- Marketplace descriptions rewritten: shorter, factual, and with the v1 limitations stated. Removed
  unsupported comparisons with other packages, search-ranking claims, cost-per-post estimates and
  categorical "never" claims that the code did not support.
- The public changelog had stopped at 1.2.2 and is now in step with the packages.
- Every README and doc now carries an explicit **Limitations** section: root-only forum placement,
  single-culture comments, unmoderated DMs, forum-only front-end queue, per-process settings cache,
  and the places that are not paged yet.
- Removed the page-comments screenshot whose sample comments read like product endorsements.

## 1.3.0 - render inside your own site layout, plus composer and toolbar fixes

Layout:
- **The community can now render inside one of your site's master templates.** Turn on
  "Inherit Host Layout" in Forum Settings and pick the master from the new **Host Master Template**
  picker, which lists the templates on your site. The community keeps its own masthead, navigation
  and sidebar; your header, footer and chrome wrap around it.
- Per community, so a multi-site install can point each one at its own site's master.
- Safe by default: if inheritance is off, no template is chosen, the chosen template has been renamed
  or deleted, or one of the community's own templates is picked, the standalone layout is used instead
  of throwing a "layout not found" error.
- When you inherit, your master template owns `<head>`. Render
  `~/Views/Partials/Community/_CommunityHead.cshtml` inside it to keep the forum's title, canonical,
  meta description and Open Graph tags. JSON-LD structured data is emitted either way.

Composer and posting:
- Members who have not yet verified their email address no longer see a working composer. Previously
  the composer rendered and accepted a draft, then the post was rejected on submit. They now get a
  clear prompt to verify instead, on both the board and the thread page.
- The composer toolbar uses inline SVG icons instead of emoji and punctuation glyphs, so the buttons
  match each other, follow your brand colour and render identically on every platform.

Front end:
- The "Start a thread" shortcut is no longer shown to signed-out visitors, who cannot use it.

SEO:
- **Fixed: JSON-LD structured data was not being recognised.** The `<script>` tag rendered, but
  Razor was encoding the `+` in its type attribute, so it was served as
  `application/ld&#x2B;json` and search and answer engines ignored it. `DiscussionForumPosting`
  and `QAPage` markup now ships with the correct media type. Present since the structured data
  was introduced.

Housekeeping:
- The backoffice package manifests now report the correct package version (they had drifted two
  releases behind).
- READMEs and Marketplace listings describe the current feature set, including the front-end
  moderation queue, GDPR account deletion, direct messages, notifications, mentions, tags and polls.

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

---
name: design-invitation
description: >-
  Design or restyle a Fotify digital invitation: template, colors, fonts,
  section order, cover image (imported from a link or uploaded by the
  organizer), template wording and the postcard, shared as a no-login preview
  link and published only after the organizer approves. Use when the user
  says "design my invitation", "make it ivory and sage", "use this photo as
  the cover", "change the template", "show me the designs", "show me a
  preview" or "publish the design" for their Fotify event. Also covers
  branding and the custom domain.
---

# Design the invitation on Fotify

Design work happens in drafts with a revision history. Guests see nothing
until `design_apply_revision` runs, so iterate freely, but never publish
without the organizer's go-ahead.

## Step 1: Read the current state

1. `get_event` for the `invitationId` (if null, `create_invitation` first).
2. `design_get_draft`: the current draft, live section settings, revision
   history and the event's asset library (images usable as the cover or in
   the carousel).
3. `design_get_capabilities` for the vocabulary: templates, palette and
   typography options. On its own it summarizes each template, so call it
   again with `templateId` before patching a template: the draft's template
   from `design_get_draft`, and again for any template you switch to. That
   call returns the template's colour knobs, the cover settings it honours
   and its text slots. `design_list_recipes` for ready-made compositions.

## Step 1b: Let the organizer pick from the gallery

When they want to choose or change the look and have not described a precise
design, call `design_browse_gallery` with the `invitationId` (and `occasion`
when you know it). In clients that render MCP Apps it opens a visual gallery
of every ready-made design, Studio look and Palette style: the organizer taps
one to try it on their draft and sees the preview in place, and you are told
what they tried. Elsewhere, relay a short list of the best matches. Picking
from real designs beats composing one from scratch.

## Step 2: Agree on a direction

Turn the brief into a concrete plan before writing: template or recipe, two
to four colors as hex values, typography mood, cover image, section order and
headline copy. Say it in two or three sentences and let the organizer adjust.
If the brief is thin ("make it nice"), ask at most two questions: mood, and
colors or a reference.

## Step 3: Build the draft

- Start from a recipe: `design_apply_recipe` replaces the composition (a new
  revision; earlier ones stay in history).
- Edit: `design_update_draft` with a partial `patch` and a short `summary`.
  Only keys in the patch change; the tool description lists them. A
  section's settings object is sent whole, and lists inside a section
  (FAQs, milestones, accommodation, custom sections) replace on write, so
  resend existing entries with new ones.
- Every draft write returns a `previewUrl` for exactly that revision.
- Cover and images:
  - A photo the organizer has on their device (or attached in this chat,
    which you cannot pass to a tool): `create_asset_upload_link`, share the
    link, wait until they say they are done, then `design_get_draft` shows
    the new photos first.
  - A public https link: `import_asset_from_url` (JPEG, PNG or WebP up to
    10 MB).
- Template wording (section eyebrows, button labels, reply-card titles):
  `get_content_slots` for the slot ids and limits, then `set_content_slots`.
  The organizer's own welcome message is `set_invitation_message`.
- Keep copy in the organizer's language and voice. Names, dates and venue
  details come from the event, never from you.

## Step 4: Preview, approve, publish

1. Show the draft with `design_show_preview` (latest revision by default):
   in clients that render MCP Apps it is an inline preview with a phone /
   desktop toggle and a Publish button that asks you to publish it. Where it
   cannot render, share its `previewUrl` (or the one from the last draft
   write, or `design_create_preview_link`). It opens on any device without
   login and expires after 7 days.
2. Loop on Step 3 until the organizer is happy.
3. Only on explicit approval, `design_apply_revision` with the approved
   `revisionId`. Confirm what went live.
4. If a preview link reached the wrong person, `design_revoke_preview_links`
   invalidates all of them without touching drafts or the live invitation.

## Motion

How the invitation moves for guests is set apart from the draft: both calls
below save to the live invitation at once (there is no revision to approve),
so agree on the change with the organizer before making it.

- The whole page: `set_display_preferences` with `motionLevel`: `full` (the
  design's entrances and opening sequence), `subtle` (gentle fades only) or
  `off` (a still page). `get_display_preferences` reads the current level.
- One section's entrance as guests scroll to it: `set_section_effect` with
  the section id and `none`, `fade-in`, `fade-up`, `zoom-in`, `slide-left`,
  `slide-right` or `blur-in`; `default` puts the design's own entrance back.
  The page's motion level still applies on top (at `subtle` every entrance
  is a fade, at `off` nothing moves).

## Postcard

The postcard slides out of the envelope, can top the invitation emails and
becomes the link preview when the invitation is shared.

1. `get_postcard_settings` shows the current card and where it is used.
2. Get the image into the asset library first, as in Step 3 (a photo from
   the organizer's device or a public https link). A portrait image works
   best.
3. `set_postcard_settings` with that image's `assetId` and where to use it.
   Nothing changes until this call, so agree on the image first.

## Related

- Branding (name, colors, watermark, social links): `get_branding` /
  `set_branding`. Match & Connect sponsor strip:
  `get_match_connect_branding` / `set_match_connect_branding`.
- Custom domain (plans that include it): `get_custom_domain`,
  `set_custom_domain` returns the DNS record to create,
  `retry_custom_domain_verification` after DNS is set. `remove_custom_domain`
  breaks every link on that domain; pass `confirm: true` only after explicit
  agreement.
- The upload page and public album look are in the photo-gallery skill.

## Do not

- Do not call `design_apply_revision` in the same turn as the edits unless
  the organizer already approved that exact revision.
- Do not overwrite section lists without resending existing items.
- Do not change the motion level or a section's entrance as a side effect
  of a draft edit: those calls change the live invitation at once.

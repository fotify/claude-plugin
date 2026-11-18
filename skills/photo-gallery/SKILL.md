---
name: photo-gallery
description: >-
  Manage the guest photo and video gallery of a Fotify event: browse uploads
  and stats, work through the moderation queue, reorder, organize into albums
  with share links, request a full ZIP export, style the public album and the
  QR upload page, and run Social Media Mode and the Gaming Center. Use when
  the user asks how many photos guests uploaded to their Fotify event, to
  approve pending photos, put shots in an album, export all photos, customize
  the upload page, pin an announcement to the guest feed or start a photo
  game.
---

# Manage the photo gallery on Fotify

Guests scan the event QR code, land on the upload page, and their photos and
videos go into the gallery, optionally through a moderation queue. Every tool
here takes the `eventId` from `list_events`.

## Know the upload window

Guests can upload only while the event is live (started and not finished).
The gallery stays viewable for a plan-dependent period after the event is
finished and is then permanently deleted. If "uploads stopped working",
check the status with `get_event` first; starting or resuming the event is
in the event-day skill.

## Browse and report

- `get_photo_stats`: totals, approved vs pending, plan limit.
- `list_photos` is paginated; filter with `albumId`, `mediaType` ("photo" or
  "video"), `author` (part of the uploader's name), `sortBy` and `sortOrder`
  rather than paging through everything. `total` counts every match, so "how
  many photos did Ana upload?" is one call with `author` and `pageSize` 1.
- Photo `url` values are signed links that expire within minutes: do not
  paste them; point the organizer to the album instead.
- Give upload times in the event's timezone.

## Moderate

- `list_photos_to_review` is the queue (only when manual approval is on);
  `approve_photo` per item.
- `delete_photo` is permanent for guests and organizer alike. Pass
  `confirm: true` only after the organizer explicitly agreed to delete that
  item. Never bulk delete on a vague instruction; name what would go first.
- `reorder_photo` changes the custom order.

## Albums

- `list_event_albums`, `create_event_album`, `update_event_album`.
  `delete_event_album` keeps the photos (they return to the main gallery) but
  its share link stops working; confirm first.
- `move_photos_to_album` moves up to 100 photos or videos into an album, or
  back to the main gallery when `albumId` is omitted. Ids that do not belong
  to the event come back as not moved.
- `set_album_sharing` turns an album's public link on (hand it over) or off.
  Turning it off kills every copy of the link already shared, and turning it
  back on creates a different link, so confirm before switching it off.

## Export

`request_photo_export` zips the whole gallery and emails a download link,
valid 7 days, to the organizer's own account address (it cannot go anywhere
else). One export per event per 24 hours; a repeat call returns the queued
one. Paid plans only; if the plan does not include it, say so.

## Public album and upload page

- Public album: `get_album_customization`, then `update_album_customization`
  for colors, description and what guests may do (download, share, react,
  comment). Passwords stay in the dashboard.
- Upload page (what the QR code opens): `get_upload_page` first; it lists the
  templates and current customization. Then `update_upload_page` with only
  the keys to change. Switching template drops manual color, font and layout
  tweaks, so warn the organizer if they tuned it by hand.
- Upload form: `set_upload_page_form` sets the name and message inputs and
  up to 10 custom questions. It takes the complete list: resend every
  existing question with its id plus your change. A dropped question deletes
  the answers guests already gave.

## Social Media Mode and games

- Guest feed: `get_social_media_mode`, `set_social_media_sharing` (turning it
  off kills the shared feed link; confirm first), `set_social_media_settings`,
  `publish_social_pinned_post` (optionally with an image from a link) and
  `unpin_social_post`.
- Gaming Center: `set_games_enabled`, `list_games`, `create_game`,
  `set_game_status` (open, close, publish results) and `get_game_results`.

## Do not

- Do not delete or bulk-move anything the organizer did not clearly identify.
- Do not switch the upload page template without warning about lost tweaks.

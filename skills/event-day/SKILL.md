---
name: event-day
description: >-
  Run a Fotify event on the day: start, pause or resume guest photo uploads,
  check guests in at the door and see who has arrived, and drive the live
  photo slideshow on the venue screen (pause, skip, pin a photo, flash an
  announcement). Use when the user says their Fotify event is starting, asks
  to open or pause uploads, check someone in, how many guests have arrived,
  or to control the slideshow or show a message on the screen.
---

# Run the event day on Fotify

Resolve the `eventId` with `list_events` and, for check-in, the
`invitationId` with `get_event`.

## Start, pause or resume uploads

- `get_event` reports the status: upcoming, live, paused or finished.
- `set_event_status` with "live" starts an upcoming event (guest uploads
  open and the plan's upload window starts counting now; pausing later does
  not stop that clock) or resumes a paused one. "paused" closes uploads until
  it is live again. Confirm before starting early, because the window starts
  immediately.
- This does not touch the invitation or RSVPs; those pause with
  `set_invitation_status`.
- Finishing the event is done in the dashboard, not here.

## Check guests in at the door

1. `get_attendance_settings`: QR check-in must be on (each confirmed guest
   then has a personal entry QR code, and staff scan it from the check-in
   page). Turning it on with `set_attendance_settings` needs a staff password
   that is set in the dashboard's Check-in tab; if it is missing, say so.
2. `check_in_guest` marks an arrival by hand, exactly like a scan: pass the
   guest-list entry id from `list_guests` as `rsvpId`, and `guestId` to tick
   one named person in the party (without it the whole party is marked).
   `checkedIn: false` undoes it. The party must have accepted.
3. `get_attendance_overview` answers "how many have arrived": checked-in
   parties and people versus expected, plus the latest arrivals.

## Slideshow on the venue screen

- `get_carousel_settings` / `update_carousel_settings` for how the slideshow
  looks and behaves.
- `carousel_remote` sends a live command: pause, resume, skip, show the
  upload QR code, pin one photo, flash an announcement for a few seconds, or
  scatter an emoji reaction. The tool description lists the commands and
  their options. Pause and pin persist on every screen until undone, so
  remind the organizer, or resume and unpin when the moment is over.

## Keep an eye on uploads

Moderation during the event (`list_photos_to_review`, `approve_photo`) and
pinned posts on the guest feed are in the photo-gallery skill.

## Do not

- Do not start the event before the organizer says so.
- Do not check people in who have not arrived; ask when a name is ambiguous.

---
name: event-report
description: >-
  Give the organizer a read-only status report on a Fotify event: RSVP
  breakdown and who is still pending, photo uploads and the moderation
  backlog, email delivery, arrivals at the door, budget progress, games,
  gifts, sub-event answers and registration links. Use when the user asks
  how their Fotify event is going, wants a status report, who hasn't
  answered, how many photos came in, whether the invitations were delivered,
  or how many guests have arrived.
---

# Report on a Fotify event

Read-only. Pull the numbers, present them compactly and suggest at most two
next steps. Resolve the `eventId` with `list_events` (ask which event when
several are active).

## What to pull

1. `get_event_engagement`: photo uploads, game participation and the RSVP
   breakdown. This one call answers a quick "how is it going".
2. For a fuller report add only what the question needs:
   - Guests: `get_guest_stats` (accepted, declined, pending, plan limit) and
     `list_guests` filtered by status for the names still pending.
   - Emails: `get_email_stats` and `list_scheduled_email_sends`.
   - Photos: `get_photo_stats` and `list_photos_to_review` for the backlog.
   - Arrivals: `get_attendance_overview` (needs the `invitationId` from
     `get_event`).
   - Money and tasks: `get_planning_overview` (spent vs target, next
     payments due, overdue tasks, open decisions).
   - Sub-events: `list_sub_events`, then `list_sub_event_responses`.
   - Registration links: `list_registration_links` (views, registrations).
   - Gifts: `get_gift_engagement`. Games: `list_games`, `get_game_results`.
   - VIP perks: `get_treatment_assignments`.

## How to present it

- Lead with the number the organizer cares about ("62 of 90 invitations
  answered, 48 attending").
- Use a short table for breakdowns. List names only where asked (pending
  guests, bounced emails), never the whole guest list.
- Give times in the event's timezone and money with its currency.
- Close with at most two next steps, each tied to a workflow: remind pending
  guests (send-emails), clear the moderation backlog (photo-gallery), seat
  newly confirmed guests (seating-chart), pay what is due (planning-hub).

## Do not

- Do not act on the suggestions in the same turn; the report is read-only
  until the organizer asks for the follow-up.
- Do not compute totals from one page of `list_guests`; use the stats tools.

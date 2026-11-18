---
name: plan-event
description: >-
  Create and configure a Fotify event end to end: the event itself, its
  digital invitation, RSVP settings, deadline and questions, welcome message,
  schedule, venue map, event details (dress code, FAQs, accommodation,
  transport), story timeline, sub-events and VIP perks. Use when the user
  wants to create an event on Fotify, set up their wedding, birthday or
  conference there, turn on RSVP, add a question to the RSVP form, build the
  schedule, add a rehearsal dinner, or change the date, time, venue or
  timezone of a Fotify event.
---

# Plan an event on Fotify

Goal: an event whose invitation, RSVP and schedule match what the organizer
described, plus the links they need to share. Work in this order and stop to
ask whenever a required detail is missing.

## Step 1: Gather the essentials

Ask for anything not given yet: what the event is, the name guests will see,
date, start time, venue (name or address), the venue's timezone, rough guest
count, and whether guests should RSVP. Do not guess a date or timezone.

## Step 2: Create the event

1. `list_event_types` to see the plans. Read what each plan can do from its
   features and `creditCost` (0 is free; anything else spends that many of
   the organizer's event credits). Use the plan the organizer named. If they
   did not name one and more than one fits, ask; never spend credits on a
   plan they did not choose.
2. `create_event` with `name`, `eventTypeId`, `eventDate` (YYYY-MM-DD),
   `eventTime` (HH:MM), `timezone` (IANA), `location` and `description`. Set
   `enableRsvp: true` when they want responses; that also creates the
   invitation. Pass `useSubscription: true` only when the organizer says the
   event belongs to their subscription.
3. If the event already exists, find it with `list_events` and use
   `update_event` with only the fields that change.
4. If `create_event` is refused for credits or the number of free events,
   relay the reason plainly. Do not retry on another plan or with
   `useSubscription` on your own.

## Step 3: Invitation and RSVP

1. `get_event`. If `invitationId` is null, `create_invitation` (optionally
   with `rsvpDeadline` as the last day guests can answer).
2. Switches: `set_rsvp_enabled`, `set_rsvp_deadline` (must be in the future;
   to stop answers now use `set_rsvp_enabled` with `enabled: false`, or
   `set_invitation_status` to pause the form while details change),
   `set_rsvp_capacity` to close acceptances once the venue is full
   (counts people, companions included; `null` removes the cap),
   `set_rsvp_admission` to make self-registrations wait for the organizer's
   approval, or to open a waitlist once the cap is reached. Filling freed
   places from the waitlist automatically (`waitlistAutoPromote`) emails
   each guest let in, starting with places already free, so switch it on
   only after the organizer agreed to those emails,
   `set_rsvp_comments` for the free-text box, `set_guest_names_enabled` to
   name each companion, `get_rsvp_notifications` / `set_rsvp_notifications`
   for who is emailed every answer.
3. Questions: `get_custom_fields` first, because `set_custom_fields` replaces
   the whole list. Keep existing field ids so earlier answers survive. The
   tool description lists the field types and options.
4. Wording: `get_invitation` returns the current texts. Rewrite the welcome
   message with `set_invitation_message` (send the complete message and copy
   unchanged parts exactly), RSVP labels with `set_rsvp_texts`, date/time
   format, a fixed guest language and how much the page moves (`motionLevel`)
   with `set_display_preferences`.

## Step 4: Schedule, venue and details

- Schedule: `get_itinerary`, then `add_itinerary_entry` per entry; edit with
  `update_itinerary_entry`, remove with `delete_itinerary_entry`.
- Venue on the map: `search_places`, show the organizer the candidates, then
  `set_location` with the chosen place. `set_location_instructions` adds
  directions; `remove_location` clears the map.
- Details: `get_event_details_settings` then `set_event_details_settings` for
  dress code, FAQs, accommodation and transport.
- Story timeline: `get_story_settings`, `update_story_settings`,
  `add_story_milestone`, `update_story_milestone`, `reorder_story_milestones`,
  `delete_story_milestone` (it also deletes the milestone's photo; ask first).
- Sections with a get/set pair: countdown (`set_countdown_settings`), calendar
  button (`set_calendar_settings`), envelope (`set_envelope_settings`), cover
  text (`set_hero_settings`), guest photos on the invitation
  (`set_photo_upload_settings`), gifts (`get_gift_settings`,
  `set_gift_settings`), song requests (`set_playlist_settings`) and custom
  sections (`get_custom_sections`, `set_custom_section`). Read before
  writing; lists inside a section replace on write.
- Layout: `update_section_order`, and `set_section_visibility` to hide or
  show a section without losing its content.
- Save-the-date page: `get_save_the_date` / `set_save_the_date`.

## Step 5: Multi-part programs and VIP perks

These need a plan with the matching feature (see `list_event_types`) or a
subscription; if the tool says the plan does not include it, explain that.

- Sub-events (rehearsal dinner, brunch): `create_sub_event` with `name`,
  ISO `startsAt` / `endsAt`, `timezone`, capacity and who may see it
  (everyone, guests with certain tags, or guests you add with
  `set_sub_event_guest_access`). `update_sub_event` edits it and
  `list_sub_event_responses` shows answers. `delete_sub_event` removes it
  with every answer; pass `confirm: true` only after explicit agreement.
- Special treatments (hotel deals, transport, discount codes):
  `create_special_treatment`, then `assign_treatment_to_guests`;
  `generate_treatment_codes` for unique code pools. You see counts, never the
  code values.

## Step 6: Hand over

Give the invitation link (`get_invitation`) and the photo upload link
(`get_upload_page`). If RSVP is on, explain that every guest gets a personal
RSVP link once they are on the guest list (guest-list skill). Offer the
design step next (design-invitation skill).

## Do not

- Do not create a second event with the same name; ask whether to reuse it.
- Do not send any email from this workflow; sending always previews first
  (send-emails skill).
- Do not invent venue details. If `search_places` finds nothing, ask for the
  address.

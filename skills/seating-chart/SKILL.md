---
name: seating-chart
description: >-
  Build and edit the seating chart of a Fotify event: create tables, rebuild
  the venue floor plan (tables, stage, dance floor, bar, walls, labels) from a
  photo or description, and seat every guest and plus-one automatically or by
  hand. Use when the user asks to create tables, seat their guests, make a
  seating chart or table plan for their Fotify event, shares a photo of the
  venue floor plan, says "put the Smiths at table 4", or wants to show or
  hide table assignments or the find-my-seat page.
---

# Build the seating chart on Fotify

Seating is per person, not per guest entry: every party member and plus-one
has their own row in `list_seating_guests`. Seating works without a
guest-facing RSVP.

## Step 1: Get the invitationId

`setup_seating` with the `eventId` returns the `invitationId` the seating
tools need. It creates the seating container when the event has no
invitation, never switches on RSVP, and is safe to call again.

## Step 2: Create tables

- `list_tables` first to see what exists.
- Several tables of the same size ("8 tables of 10"): one `create_tables` call
  with `count` and `capacity` (1-100), optional `shape` and chair layout. The
  numbers continue after the highest table, or start at `startNumber`; if a
  number is already taken, nothing is created. Ask if the count or size is
  unclear.
- A single or different table ("the head table"): `create_table` with
  `capacity`, optional `alias`, `shape` and floor-plan placement. `number`
  is optional: omitted, it takes the next number after the highest table.
- Creating the first table turns guest-facing seating on. While the organizer
  is still arranging, `set_hide_table_assignment` with `hidden: true` keeps
  assignments off the invitations, and `set_seating_enabled` with
  `enabled: false` hides the chart entirely.
- Recommend `set_guest_names_enabled` so seats show names instead of numbered
  placeholders.
- `update_table` edits a table. `delete_table` unseats everyone at it (they
  stay on the guest list); confirm first when people are seated there.

## Step 3: Rebuild a floor plan from a photo or description

The canvas size and axes are in the `create_table` and
`create_venue_element` descriptions: positions are the element's center,
origin top-left, x grows right, y grows down.

1. Look at the image. Identify tables (count, shape, rough capacity) and
   fixtures: stage, dance floor, bar, buffet, DJ, entrance, restrooms, walls,
   text labels.
2. Scale the drawing proportionally onto the canvas, keep relative positions
   and leave margins.
3. Create tables with positions, and fixtures with `create_venue_element`
   (type from the tool description; long thin rotated rectangles of the wall
   type for room outlines).
4. Adjust with `list_venue_elements`, `update_venue_element` and
   `delete_venue_element`.
5. Tell the organizer what you placed and that positions are approximate;
   they can drag to fine-tune in the dashboard.

## Step 4: Seat the guests

**Automatic** (most requests): `auto_seat_guests` with the `invitationId`.
It keeps parties together, clusters guests who share a tag, never seats
declined guests and leaves reserved chairs free.

1. Run it without `apply` first. That is a dry run: it returns who would sit
   where and who does not fit.
2. Map the organizer's rules to its options: `includePending` to also seat
   guests who have not answered, `keepExisting: false` to reshuffle people
   already seated, `groupByTags: false` to mix groups. When the organizer
   names a group and tables ("put the VIPs at tables 1, 2 and 5"), pass
   `tags: ["VIP"]` and `tableIds: [1, 2, 5]`: tables go by number, name or
   id, tags by name or id, and only that group is seated, only there. Check
   `scope` and any hint in the result: a table or tag that matched nothing
   is named there, and whoever does not fit is in `unplaced`.
3. Show the proposed plan compactly and ask. Only after the organizer agrees,
   call it again with `apply: true`.

**By hand** (one-off moves, or rules auto seating cannot express):
`list_seating_guests` (it syncs new people from the guest list first; it
returns 50 people per call, so narrow it with `tag`, `tableId`, `unseated`
or `status`, and follow `nextOffset` while `hasMore` is true), then:

- A group or a party at one table ("put the Smiths at table 4"):
  `assign_party_to_table` with the `tableId` and either their
  `seatingGuestIds` or the party's `guestId` (everyone in it who is coming).
  The table's free seats are checked for the whole group first; if they do
  not all fit, nobody moves and the error says how many seats are free.
- One person, or a specific chair: `assign_guest_to_table` with their seating
  id, the `tableId` and an optional 0-based clockwise `seatIndex`. Omit
  `tableId` to unseat. Taking an occupied seat moves the previous occupant to
  "no specific seat" at the same table.

Report the result as a short list per table: number, alias, seated /
capacity, names. Mention anyone left unseated and why.

## Step 5: Publish

- `set_hide_table_assignment` with `hidden: false` shows each guest their
  table on their invitation.
- `set_seat_finder_enabled` turns the public "find my seat" page on or off
  (handy as a QR code at the entrance). It is independent of the per-guest
  setting above.
- `set_seat_finder_map` (off by default) adds a venue map to that page: a
  guest who finds their name sees the room as drawn on the Seating map with
  their table highlighted. Only tables placed on the map appear; aliases and
  the floor-plan photo never do. Suggest it for large venues, and remind the
  organizer that map labels become visible to guests.

## Do not

- Do not apply an automatic plan the organizer has not seen.
- Do not seat people the organizer excluded (declined guests, unless told).
- Do not guess a plus-one's name; leave the placeholder as is.

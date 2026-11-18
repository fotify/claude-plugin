# Fotify for Claude

Fotify is an event platform for weddings, parties and corporate events.
Organizers send digital invitations with RSVP, and guests scan a QR code to
share their photos in a live gallery. This plugin connects Claude to your
Fotify account so you can run your event from a conversation.

## What you can do

- Create events and their digital invitation, set the RSVP deadline and
  questions, and build the schedule and venue details.
- Import a guest list pasted from a spreadsheet, tag guests and export the
  list with each guest's personal RSVP link.
- Create tables, rebuild the floor plan from a photo of it, and seat guests
  with families kept together.
- Restyle the invitation as drafts and review each one on a preview link
  before anything goes live.
- Customize, test and schedule invitation emails. Every send shows who will
  receive it and waits for your confirmation.
- Moderate, organize and export the photos guests upload, and run the photo
  slideshow.
- Keep the budget, vendors, checklist and decisions up to date.
- Check guests in at the door and see who has arrived.

## Get started

1. Add the plugin, then connect the Fotify connector from the plugin's
   Connectors tab (in Claude Code it connects when the plugin loads).
2. Sign in to Fotify in your browser and approve the permissions screen.
3. Ask, for example: "Show me my Fotify events and what you can help me
   with", "Add these guests to my wedding and tell me who is missing an
   email address", or "Make my invitation ivory and sage and show me a
   preview before publishing".

You need a Fotify account (https://fotify.app). Claude works with the
events in the account you sign in with, and with what that account already
has; plans and account settings stay in the Fotify dashboard.

## What the plugin contains

- Ten skills (`skills/`) that tell Claude which Fotify tools to use for each
  workflow and which steps need your confirmation: get-started, plan-event,
  guest-list, seating-chart, design-invitation, send-emails, photo-gallery,
  planning-hub, event-day and event-report.
- One remote MCP server (`.mcp.json`): `https://server.fotify.app/mcp/claude`,
  run by Fotify. The plugin runs no local code, scripts or hooks.

## Safeguards

- Invitation, save-the-date and reminder emails are only sent after Claude
  has shown you who will receive them and you have confirmed; Fotify's
  servers enforce this. Claude also asks before an RSVP approval or decline
  emails the guest, or before switching on automatic waitlist emails.
- Invitation designs stay in drafts until you approve publishing one.
- Claude asks before deleting anything.
- Uploading files from your device and setting passwords stay in the Fotify
  dashboard.

## Data and privacy

The plugin sends the requests you make, and the event, guest, design and
photo details they need, to your Fotify account through
server.fotify.app over HTTPS. It sends nothing to any other service and
stores nothing on your computer. Fotify stores your event data as described
in its privacy policy: https://fotify.app/privacy-policy. You approve the
permissions when you connect and can disconnect at any time from your Fotify
account.

## Support

- Help center: https://fotify.app/help
- Email: support@fotify.app
- Terms of use: https://fotify.app/terms-of-use

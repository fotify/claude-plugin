---
name: send-emails
description: >-
  Customize, test, send and schedule the emails of a Fotify event
  (save-the-date, invitation, reminder, update, cancellation, thank-you) with
  the mandatory preview and confirmation before anything reaches a guest, and
  prepare personal WhatsApp or text messages the organizer sends from their
  own phone. Use when the user asks to send or schedule their Fotify
  invitations, email the guests who haven't answered, change the wording of an
  invitation email, send themselves a test, cancel a scheduled send, get
  WhatsApp links for guests, or asks about email delivery, opens, bounces or
  email credits.
---

# Send and schedule emails on Fotify

Emails reach real people and cannot be recalled. The server refuses to send
without a `confirmationToken` from a preview made in the last 10 minutes.
Your job is to make the numbers visible and get an explicit yes.

## Customize a template

1. `list_email_templates`: template types, current copy, visual themes,
   supported languages and which templates are on.
2. `update_email_template` with the `templateType` and only the fields being
   changed (subject, title, intro, message, button text, sender and
   organizer names, reply-to, theme, default language, on/off; on the
   reminder template the automatic RSVP reminder and how many days before the
   deadline it goes out; on the thank-you template where its button goes,
   `ctaTarget`: "upload" for the upload page, the default, "album" for the
   public album, or "none" for no button). Content is moderated; if it is
   rejected, rewrite rather than retry.
   - "album" needs public album sharing on; while it is off, the email goes
     out without a button, so tell the organizer.
   - "album" changes only where the button leads. While the event is live,
     guests can still add photos from the album if its upload button is on,
     so an organizer who wants no more uploads also finishes the event or
     turns that button off.
3. Offer `send_test_email` after meaningful changes. It goes only to the
   organizer's own inbox, costs no credits and is limited to 3 per 10
   minutes.

## Send to guests: preview, confirm, send

1. `preview_invitation_send` with the `eventId`, the `templateType` and the
   guests the organizer described: an `audience` from the tool description
   (for example "not_answered" for guests who have not replied), `tagIds` for
   a group, or `guestIds` for specific people. Add `skipAlreadySent: true`
   when finishing a send that partly failed. Nothing is sent by this call.
2. Tell the organizer plainly how many guests will receive it, how many are
   skipped and why (no email, opted out, already sent) and the remaining
   email credits. Ask: send now, schedule it, or change who gets it?
3. Only after an explicit yes to that preview:
   - now: `send_invitations` with the `confirmationToken`;
   - later: `schedule_email_send` with the token and `scheduledAt`, an ISO
     timestamp for the time the organizer means in the event's timezone (at
     least 1 minute and at most 1 year ahead).
   If the token expired, preview again and re-confirm; the numbers may have
   changed.
4. Report what was sent or scheduled. `list_scheduled_email_sends` and
   `cancel_scheduled_email_send` manage pending sends (cancelling needs the
   organizer's go-ahead).

## Personal messages from the organizer's phone

For guests without an email, or when the organizer prefers WhatsApp:
`get_share_messages` returns the organizer's short invitation message
personalized per guest (name and personal invitation link filled in) plus a
ready WhatsApp link for guests with a phone number. Filter by `status`, tags,
or `onlyWithPhone`. Fotify sends nothing here; the organizer taps each link.
`set_share_message` rewrites the text: keep the personal link placeholder
from the tool description in it, and show the organizer the new text first.

## Delivery report

`get_email_stats`: sends by template and status (sent, delivered, opened,
bounced), guests reachable by email, opt-outs and remaining credits. Combine
with `list_guests` when the organizer asks who exactly bounced or never got
an email.

## Do not

- Do not treat an earlier general instruction ("send the invites when
  ready") as confirmation of a specific preview. Confirm the numbers.
- Do not try to send a test to anyone but the organizer.
- If email credits are short, say how many are missing and stop. This plugin
  cannot add credits; they are managed in the organizer's Fotify account.

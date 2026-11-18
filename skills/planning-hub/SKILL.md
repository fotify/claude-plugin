---
name: planning-hub
description: >-
  Run the planning hub of a Fotify event: budget categories and lines,
  deposits and payment due dates, vendors, the dated checklist and the
  decisions board organizers vote on. Use when the user asks to set up the
  budget or planning for their Fotify event, add a cost, record or schedule a
  payment, add or book a vendor, see what is due, add or complete a checklist
  task, compare options ("band or DJ?") or decide between them.
---

# Run the planning hub on Fotify

The planning hub keeps the money, vendors, tasks and open decisions of an
event in one place, shared with the organizer's collaborators. It needs a
plan that includes planning or a subscription; if a tool says the plan does
not include it, explain that.

## Money is in minor units

Every amount is an integer in the minor unit of the workspace currency
(cents): 1,250.00 is 125000. Convert both ways and show amounts to the
organizer with the currency, never as raw integers.

## Step 1: Set up or read the hub

1. `get_planning_overview` with the `eventId`. When it returns no workspace,
   planning is not set up yet.
2. `setup_planning` picks a template (wedding, party, corporate or generic)
   that seeds budget categories, payer labels and, unless `seedChecklist` is
   false, a dated checklist relative to the event date. Ask for the currency
   (ISO code), the expected guest count and the total budget target if the
   organizer has them.
3. `update_planning_settings` changes currency (amounts are relabeled, not
   converted), expected guests, the target, or the payers list (send the
   full list; keep existing ids).

## Budget

- `get_budget` lists categories, lines, payments, vendors and payers.
- `add_budget_item` in a category with estimated and/or contracted amounts.
  Use the per-guest flag for catering or rentals so the projection follows
  the confirmed headcount. Link a payer and a vendor when known.
- `update_budget_item` changes only the fields given. `delete_budget_item`
  also deletes its payments; ask first.
- Payments: `add_budget_payment` schedules or records a deposit, installment
  or balance (`dueDate` is a YYYY-MM-DD venue calendar day; mark it paid when
  it already is). The organizer gets email reminders a week and a day before
  each unpaid due date. `update_budget_payment` marks it paid or changes it.

## Vendors

`list_vendors`, `add_vendor` (status enquired, quoted, booked or paid; the
on-site arrival time for the day's run sheet is venue wall-clock),
`update_vendor`.

## Checklist

`list_planning_tasks`, `add_planning_task` (YYYY-MM-DD due date; the assignee
must be the organizer or an accepted collaborator, listed in the overview),
`update_planning_task` to complete or change a task.

## Decisions

- `list_decisions` shows open and decided questions with their options,
  prices, votes and the organizer's own vote.
- `add_decision` adds a question ("Which venue?") with up to 10 options,
  each with an optional price, pros, cons and vendor.
- `decide` picks an option. With a budget category it also creates the
  budget line at that price and moves its vendor to booked. Confirm with the
  organizer before calling.

## What to report

For "where are we", lead with spent vs target and what is due next (from
`get_planning_overview`), then overdue items and open decisions. Keep it to
a few lines and offer the details.

## Do not

- Do not invent prices, vendors or due dates; ask.
- Do not decide on the organizer's behalf, even when one option is cheaper.

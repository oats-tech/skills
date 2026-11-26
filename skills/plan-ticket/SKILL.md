---
name: plan-ticket
description: Plan a ticket before any code — understand the goal, agree what to build one question at a time, then write a spec and a checklist of small tasks with clear "Done when" checks.
argument-hint: ticket key or short description
disable-model-invocation: true
---

You are planning a piece of work. No product code gets written here. The output is a **spec** and a **task checklist** that `/execute-ticket` can build one task at a time.

Keep the plan in `.plans/<ticket-key>/` — with no ticket, use a short slug of the work instead (`.plans/csv-export/`) and tell the user, since the other skills take it in place of the key (add `.plans/` to `.git/info/exclude` if git doesn't ignore it). If the folder already exists, read it and continue from where it stopped.

## 1. Understand

- Read the ticket from the tracker if one is connected; otherwise ask what the work is. Read any mocks, designs or notes about it, and the code it touches, before asking anything.

Done when: you can state the goal in two sentences — what's being built, and for whom.

## 2. Decide

Ask **one** question per message. The aim is a shared picture of **what to build**: the goal, who uses it, the main flow, and what's out of scope. Each question has 2–4 options, with your recommendation first and why. If an answer conflicts with the code, say so.

Write the answers into `spec.md`:

- **Goal** — the problem and the solution, from the user's side
- **Flow** — what the user does step by step and what they see
- **How it's built** — the pieces (data, backend, API, UI) and how they connect
- **Decisions** — each with its reason
- **Out of scope**

Done when: the user agrees with `spec.md` and could explain the flow and how it's built in their own words.

## 3. List the tasks

Write `tasks.md`: a checklist of small tasks in build order. Each task should fit in one session — a few files, roughly 10–30 minutes of work. If a task needs more than 3–4 "Done when" lines, split it.

```
- [ ] 01 <title>
  What: <the change>
  Done when:
  - <checkable: a test that passes, an endpoint that returns X, a screen that shows Y>
```

Done when: every part of the spec is covered by a task and the user has seen the list.

## 4. Share

If the work has a ticket in a connected tracker, offer to post a short summary as a comment: goal, key decisions, out of scope. Show it to the user before posting.

Next: `/execute-ticket <key>` to build the tasks one by one.

## Changing the plan

When `/verify-ticket` (or anything else) shows the spec is wrong, run steps 2–3 on just the change: agree it one question at a time, update `spec.md` with a dated note saying what changed and why, and add new tasks at the end of `tasks.md`. Then `/execute-ticket` the new tasks and `/verify-ticket` again.

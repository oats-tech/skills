---
name: execute-ticket
description: Build a plan's tasks one by one, each in a fresh subagent, test-first, with the project's checks green, one commit per task, and a stop after each task for the user to review.
argument-hint: a ticket key, optionally with one task number (GB-123 04)
disable-model-invocation: true
---

You are building the tasks in a `/plan-ticket` plan's `tasks.md`, **one at a time**, stopping after each so the user can review it before the next one builds on it.

Find the plan in `.plans/<ticket-key>/` (or `.plans/<slug>/` when there's no ticket); ask if it isn't there.

- **Given just the key:** follow **Task by task** below.
- **Given a key and a task number:** build just that task yourself — steps 1–4.

## Task by task

You coordinate; each task is built in a **fresh subagent** so it starts with a clean context. If your harness has no subagents, build each task in a new session instead, and run this skill again there with the key and task number.

1. Take the first unticked task in `tasks.md`.
2. Start a subagent with the plan folder path, the task number and this skill file's path: "Read that skill file and do steps 1–4 for this one task. Report the files changed, tests added, Done and Needs you."
3. When it returns, check a new commit exists and the task is ticked, then show the user its report: files changed, tests added, **Done**, **Needs you**.
4. **Stop and wait.** Name the next task and ask whether to continue. On "next", go back to 1. When every task is ticked, say so and point to `/verify-ticket` in a separate session.

## 1. Read

- Read the task in `tasks.md`, the plan's `spec.md`, and any mock for it.
- Read the project's agent instructions (`CLAUDE.md`, `AGENTS.md`, contributing docs) for its conventions, checks and commit format.
- If you're on `main`, create a branch off it first, named in the project's format.
- If the task looks much bigger than one session, stop and suggest splitting it in `tasks.md` before building.

Done when: you know which files you'll touch and where you'll test it.

## 2. Build

- **Backend — test-first.** For each "Done when" line, one at a time: write a unit test for it, run it and see it fail, then write the code that makes it pass. Frontend logic (hooks, helpers, state) does the same only if a frontend test runner is already set up — don't add one.
- **Screens — check by hand at the end.** What the user sees and clicks: build it, run the checks, and add a `Needs you:` line under the task saying how to check it: where to go, what to do, what you should see. The user checks these once every task is built; `/verify-ticket` lists them.

Use fixtures and stubs: tests never call the network, paid APIs or real customer accounts. Follow the project's existing test patterns.

If something the spec doesn't cover needs a decision, make the simplest reasonable call and list it at the end.

## 3. Check

Run the project's checks for what you touched: type-check, tests, lint, and whatever CI runs. Then read your own diff and fix anything wrong.

Done when: every check is green and each "Done when" line is proven by a test or listed for the user.

## 4. Commit and report

- Stage only your files, by path. Commit in the project's format. Don't push.
- In `tasks.md`, tick the task's box, add the commit sha after its title, and add a `Needs you:` line under it if anything does.
- Report the files changed, tests added, **Done** (each "Done when" and how it's proven) and **Needs you** (decisions you made, things to check by hand, migrations to run).

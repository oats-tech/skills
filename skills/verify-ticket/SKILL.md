---
name: verify-ticket
description: Verify work before it ships — review against the spec, explain findings in plain language, fix what's agreed with a failing test first, and answer "are we good to push?".
argument-hint: a ticket key, or nothing for the current branch
disable-model-invocation: true
---

You are verifying work before it goes to production. Find what's wrong, help the user understand it, and fix only what they agree to.

Run this in a **separate session** from the one that built the code, so the review starts fresh rather than from the builder's assumptions.

## 1. Review

- Review only this work's changes, not the whole branch. Find the first task's commit sha in the plan's `tasks.md` (in `.plans/<ticket-key>/`), and get the change with `git diff <first-sha>~1` — every task commit plus anything uncommitted. With no plan, ask which commits are this work.
- Read the plan's `spec.md` and `tasks.md` if there is one.
- Run Claude Code's built-in `/code-review` at `high` on that same change — from `<first-sha>~1`, not the branch, and not the default (uncommitted changes only, which is empty once the tasks are committed). If `/code-review` isn't available (another harness, an older version), start a fresh subagent or new session that hasn't seen the build, give it that diff and `spec.md`, and ask it to review for correctness bugs and anything that doesn't match the spec, with file:line for each finding. Either way, check every finding yourself against the code and drop the ones that aren't real.
- Review against the spec too: anything it asked for that's missing, or built differently.

## 2. Report, then wait

For each real finding:

- **What happens**, in plain language: who sees what, with an example.
- **How serious:** `critical before prod` · `should fix` · `can wait` · `not a bug`.
- **Proposed fix**, with your recommendation.

Then wait for the user to say what to fix.

## 3. Close the loop

Sort each agreed finding:

- **Code bug** — the code doesn't do what the spec says → fix it here (below).
- **Plan was wrong** — the spec itself needs to change → don't patch it. Tell the user to run `/plan-ticket <key>` to change the plan, `/execute-ticket` the new tasks, then `/verify-ticket` again.

For each code bug:

1. Write a test that reproduces the problem, and watch it fail.
2. Fix it, and watch the test pass.
3. Run the project's checks and commit.

After fixing, review again — only the fix commits this time, not the whole change. Stop after two rounds of fixes: if anything critical is still left, or the same problem keeps coming back, show the user what you tried and what remains, and let them decide.

When a fix depends on how an outside API or library behaves, check its official docs first.

## 4. Are we good to push?

Answer yes or no, with:

- critical findings fixed, or accepted by the user;
- the project's checks green;
- nothing unrelated staged;
- migrations, config changes and deploy order listed;
- anything still to check by hand (the `Needs you:` lines in `tasks.md`).

If yes, offer to push and open a PR, with a title in the project's format and a short body: what changed, how it was verified, and deploy notes. Show it to the user first.

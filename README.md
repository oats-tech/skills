# Skills

Agent skills for shipping work with AI: a **Plan → Execute → Verify** loop.

| Skill | What it does |
|---|---|
| `/plan-ticket <key>` | No code. Reads the ticket and code, asks one question at a time until you can explain how it'll be built, then writes `spec.md` and a `tasks.md` checklist of small tasks with "Done when" checks. |
| `/execute-ticket <key>` | Builds the tasks one by one, each in a fresh subagent, test-first. Stops after every task so you can review it. |
| `/verify-ticket <key>` | Run in a separate session. Reviews the work against the spec, explains findings in plain language, fixes what you agree to (failing test first), sends wrong plans back to `/plan-ticket`, and ends with "good to push?". |

The plan lives in `.plans/<key>/` in your project (kept out of git).

## Install

Needs Node.js 22.20+ and access to this repo (your normal git or `gh` login is enough).

```bash
# all three skills, for Claude Code, in your user folder (works in every project)
npx skills add oats-tech/skills --all -g -a claude-code -y
```

Other options:

```bash
npx skills add oats-tech/skills --list                         # see what's here
npx skills add oats-tech/skills --skill plan-ticket -g -a claude-code -y   # just one
npx skills add oats-tech/skills --all -a claude-code -y        # this project only (.claude/skills/)
npx skills update -g                                           # pull the latest versions
```

Using another agent? Swap `-a claude-code` for yours (e.g. `-a codex`). `/verify-ticket` uses Claude Code's built-in `/code-review` when it's there, and falls back to a fresh review session when it isn't.

## The loop

```
/plan-ticket GB-123  →  /execute-ticket GB-123  →  /verify-ticket GB-123
        ▲                                                  │
        └──────────── plan was wrong? change it ───────────┘
```

Start with a small ticket. Connect your issue tracker (e.g. Jira) and GitHub to your agent so it can read the ticket and open the PR.

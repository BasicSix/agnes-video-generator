# 🤖 Let an AI Agent Operate This Project

> This document is the authoritative guide for driving Agnes Video Generator with a coding agent.
> The website guide and the wiki page about agent usage both summarize this page — when they
> disagree with it, this page wins.

Agnes Video Generator was built to be operated by AI coding assistants. There are three levels,
and you can stop at any of them:

| Level | What you get | Setup |
|-------|--------------|-------|
| **1. Point your agent at this repository** | The agent can deploy the project, create tasks, watch progress and debug failures | Nothing — `AGENTS.md` already ships here |
| **2. Install the companion skill set** | The agent does not have to work the project out first: three ready-made skills cover using, debugging and contributing | One command (below) |
| **3. Distribute with flint** | Every agent you use gets the same skills, and they stay in sync | One command (below) |

---

## Level 1 — Point your agent at this repository

The repository ships an [`AGENTS.md`](../../AGENTS.md) written for agents, not for humans. It contains
the environment requirements, the four-layer deployment verification checklist, the shared
conventions (log prefixes, retry model, i18n rules) and the workflows for bug fixes, regression runs
and releases.

Clone the project, then tell your agent:

> Read `AGENTS.md` in this project, install the dependencies, configure the API key `<your-key>`,
> and start the server.

Then, to confirm the deployment is real and not just "the process started":

> Run the deployment verification checks.

The agent executes the four-layer checklist (connectivity → static analysis → endpoint tests →
feature check) and reports each result.

**This works well, but it has one limitation:** `AGENTS.md` lives in the repository, so the agent
must be able to read the repository to use it. It also describes how to *change* the project as much
as how to *operate* it.

---

## Level 2 — Install the companion skill set

The companion skill set packages the everyday operations into three skills an agent can pick up
without reading a 30 KB guide:

> **Repository: [lcy362/agnes-video-generator-skills](https://github.com/lcy362/agnes-video-generator-skills)**

| Skill | Use it when you want to |
|-------|-------------------------|
| **`agnes-video-usage`** | Take the service from nothing to finished videos: choose a deployment route, configure the key, pick among the six task types, submit a task and watch it, and use Manual Mode checkpoints to have the agent rewrite the storyboard, narration, subtitles or a scene video before resuming |
| **`agnes-video-troubleshoot`** | Work out why a run failed: queue saturation, `401` domain mismatch, timeouts, wrong resolution, a sideways portrait video, or subtitles that broke after a resume — and decide whether it is a configuration problem, an upstream capacity problem, or something worth fixing |
| **`agnes-video-contrib`** | Change the project's code and get the PR through CI: the English commit rule, the frontend rebuild rule, i18n, tests and the large-version regression |

Each skill is a decision tree plus small scripts (`agnes_task.py` to create/poll/inspect a task,
`verify_deploy.sh` for the deployment checklist, `diagnose_task.py` for evidence collection,
`check_media.py` for resolution and orientation, `preflight.sh` for the local CI gate). They are
documentation and tooling only — no service code, and no credentials ever pass through them.

**Install it.** The skills live in a `skills/` subdirectory of their own repository, which is the
layout flint understands; or you can drop them into a project by hand:

```bash
# Option A — clone and copy into this project (works with any agent that reads .agents/skills)
git clone https://github.com/lcy362/agnes-video-generator-skills.git /tmp/agnes-skills
cp -R /tmp/agnes-skills/skills/* <this-project>/.agents/skills/

# Option B — let flint manage and distribute them (see Level 3)
```

Once installed, ask your agent for an outcome rather than a command:

> My task 4de1bcd26fda failed — find out why and tell me if it is worth fixing.

> The creative video came out silent. I want narration and subtitles — set it up and run it.

> Take my finished task and improve the storyboard prompts, then regenerate just the affected parts.

---

## Level 3 — One skill set, every agent

If you switch between agents — Cursor today, Claude Code tomorrow, TRAE next week — copying the
skills into each one by hand gets old fast, and a project-level copy only helps that one project.

[**flint**](https://github.com/lcy362/flint) is a local-first skills manager that solves exactly
that: it keeps your skill directories in one place and deploys them into any agent or project
directory by symlink or copy. Register this skill repository as a source once, tag it, and put it in
front of every agent you use. It has built-in support for dozens of agents (Claude Code, Cursor,
Codex, TRAE and more), and it is local-only — no cloud, no account, no telemetry.

```bash
npx flint-skills-hub          # opens the local UI on http://localhost:8787
```

---

## Where agents help most: Manual Mode

Manual Mode pauses a run at a checkpoint and lets the agent change the artifacts before continuing.
The agent reads the task's `manifest.json`, edits the file in place, previews which downstream steps
will re-run, and approves — so "polish the narration" or "fix the subtitles" becomes one job instead
of a copy-paste round trip.

Details: [Manual Mode Guide](manual_mode_guide.md) · Product guide: [Usage Guide](usage.md).

---

## Boundaries

| Surface | Purpose |
|---------|---------|
| `AGENTS.md` | For **changing this project's code** — conventions, verification, release workflow |
| The companion skill set | For **operating this project** — deploy, generate, edit artifacts, debug, contribute |
| This document | The authoritative description of both, referenced by the website and the wiki |

You need none of this to use the Web UI or the online Studio — the agent route exists for people who
would rather describe an outcome than run commands.

---

## See also

- [Getting Started](getting-started.md) — Option D is this page's shortest form, kept in sync with it
- [Usage Guide](usage.md) — modes, checkpoints, configuration
- [Manual Mode Guide](manual_mode_guide.md) — checkpoints and the external-agent protocol
- [API Reference](api.md) — the endpoints an agent drives
- [FAQ](faq.md) — including "how do I keep the skills in sync across agents?"

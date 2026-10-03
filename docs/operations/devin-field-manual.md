[Devin Field Manual](#top)

1. [01The mental model](#map)
2. [02Index, DeepWiki, Ask](#understand)
3. [03Sessions](#sessions)
4. [04Prompting well](#prompting)
5. [05The context stack](#context)
6. [06Skills, rules, playbooks](#skills)
7. [07Plugins & governance](#plugins)
8. [08Encoding house style](#house)
9. [09Environment](#environment)
10. [10Devin Review](#review)
11. [11Parallel work](#scale)
12. [12Automations](#automations)
13. [13Integrations & MCP](#integrations)
14. [14Admin: org & access](#admin)
15. [15Admin: security](#security)
16. [16Admin: usage & cost](#cost)
17. [17The improvement loop](#flywheel)
18. [18Rollout plan](#rollout)
19. [19Pitfalls](#pitfalls)
20. [20Self-check](#quiz)

Primer for the Devin admin and subject-matter expert

# Teach Devin *how your team builds*, then let it work.

Devin is Cognition's autonomous software engineer: a cloud VM with a shell, IDE and browser that takes a task, writes and tests code, and opens a pull request. Most of the value an admin adds is upstream of any single prompt: the environment it boots into, the context it loads, and the guardrails around it. This manual covers both seats, user and admin, with the emphasis on making sessions follow your development patterns.

Checked against docs.devin.ai on 3 Oct 2026 About 60 minutes to read end to end Interactive: decision tool, rollout checklist, quiz

**16 KiB**Auto-injected from the top of each AGENTS.md or always-on rule. The rest is read only on demand.

**1**Skill active at a time. Invoking another replaces it.

**\~24 h**Periodic snapshot rebuild, plus on every blueprint save.

**30 min**Idle time before a session sleeps. Sleeping sessions use no ACUs.

**≤ 3 h**Rule-of-thumb task size: if you could do it in three hours, Devin probably can.

**L / XL**Session sizes flagged unhealthy by Session Insights. Split the work.

Show chapters for

CHAPTER 01

## The mental model

Devin is a set of products on top of three shared layers. Learn the layers first; every feature is one of them showing through.

UserAdmin

Work moves left to right across four activities: **understand** the code, **do** the change, **verify** it, and **automate** the next one. Underneath, every session draws from the same three layers. The **context layer** decides what Devin knows and how it behaves. The **environment layer** decides what machine it boots into. The **governance layer** decides what it is allowed to touch and spend.

Every product in the top row reads from the three layers below. When a session misbehaves, the fix is almost always in a layer, not in the prompt.

### Component catalog

The terms you will hear most, what each one is for, and where it lives. Color bars match the lanes above.

#### DeepWiki

Sidebar → Wiki · steer with .devin/wiki.json

Auto-generated documentation per indexed repo: architecture diagrams, source-linked pages, summaries. Ask Devin uses it as grounding.

#### Ask Devin

app.devin.ai/search

Codebase Q&A with citations and a plan mode. It writes a context-rich prompt and starts a session for you. The recommended front door for real work.

#### Session

Web app · Slack / Teams · Jira / Linear · API

One task on a fresh copy of your org's snapshot. Ends in a PR, an answer, or an artifact. Billed in ACUs.

#### Devin CLI & Desktop

curl -fsSL https://cli.devin.ai/install.sh | bash

Local agent on your machine. Same plugins and skills as the cloud. `/handoff` pushes long tasks to a cloud session.

#### Fusion

CLI: /fusion · Desktop: model selector

Local-agent model family: a frontier lead plans and reviews while a cheaper sidekick implements. Cognition's recommended default for CLI and Desktop.

#### Skills

.agents/skills/\<name>/SKILL.md · or in plugins

Reusable procedures, loaded on demand by description or by `@skills:name`. The primary way to teach your workflows.

#### AGENTS.md & rules

repo root · .devin/rules/\*.md · plugin rules/

Always-on standing guidance. Keep it short; it costs context in every session.

#### Playbooks

Settings → Playbooks · attach with !macro

Reusable prompt templates for a whole task, managed in the web app with version history. Org, enterprise and system scopes.

#### Plugins

Customize → Plugins

Installable bundles of skills, rules, hooks, MCP servers and subagents. Personal, org or enterprise scope, with required / optional / forbidden lists.

#### Knowledge deprecated

migrating to Customize → Plugins → Knowledge

Older trigger-based notes. Being converted automatically into skills inside a Knowledge plugin. Write new guidance as skills.

#### Blueprints & snapshots

Settings → Environment · or .devin/blueprint.yaml

YAML describing tools and dependencies. A build turns blueprints into the snapshot every session boots from.

#### Devin Review

app.devin.ai/review · /devin review on a PR

PR review with grouped diffs, bug catcher and security scan. Reads REVIEW.md, AGENTS.md, CONTRIBUTING.md and more.

#### Automations

Sidebar → Automations

Wire Slack, GitHub, GitLab, Jira, Linear, PagerDuty, schedules or webhooks to sessions, with conditions, preflight scripts and limits.

CHAPTER 02

## Index, DeepWiki and Ask Devin

Understanding comes before doing. These three features are cheap, fast, and the best way to give a session a strong start.

UserAdmin

### Indexing is the prerequisite

Indexing is separate from environment setup. Indexing powers search and understanding (Ask Devin, DeepWiki); the environment powers execution. To index: **Settings → DeepWiki → Repositories → Add**. Devin indexes the default branch; open a repo to add more branches under **Indexed branches**. Index the branches your team actively develops on.

**Admin**

In an enterprise, an org can't index or clone a repo until an enterprise admin grants that org access under **Settings → Repositories**. Indexing needs the **Index Repositories** permission.

### DeepWiki

DeepWiki generates a wiki for each indexed repo when you connect it: architecture diagrams, source links and summaries. Use it to onboard humans as much as Devin. There is also a free public version at [deepwiki.com](https://deepwiki.com) for open-source GitHub repos, and a DeepWiki MCP server that lets other agents query it.

| Effort level | Approx. cost | Notes |
| --- | --- | --- |
| Low (default) | Free | Enterprise orgs always run at low; not configurable. |
| Medium | \~5–10 ACUs / wiki | Needs subscription or credits. |
| High | \~20–40 ACUs / wiki | Needs subscription or credits. |

#### Steering a large repo with `.devin/wiki.json`

Large repos hit built-in limits, so some folders go undocumented. Commit a `.devin/wiki.json` at the repo root. Once it exists, automatic planning is bypassed and **exactly** the pages you list are generated. List every page you want, not only the missing one. Limits: 30 pages (80 enterprise), 100 notes total, 10,000 characters per note.

.devin/wiki.json

```
{
  "repo_notes": [
    { "content": "Monorepo. services/ holds deployable APIs, libs/ holds shared code. Prioritise the billing service and its event contracts.", "author": "Platform team" }
  ],
  "pages": [
    { "title": "Architecture Overview", "purpose": "How services/, libs/ and infra/ fit together" },
    { "title": "Billing Service", "purpose": "services/billing: API, event publishing, idempotency", "parent": "Architecture Overview" },
    { "title": "Shared Libraries", "purpose": "libs/: logging, auth client, retry helpers", "parent": "Architecture Overview" },
    { "title": "Testing & CI", "purpose": "Test layout, fixtures, the CI gate and how to run it locally" }
  ]
}
```

### Ask Devin: the recommended front door

Ask Devin answers questions about your code with citations, and its plan mode scopes work before any code is written. When the plan is clear, start a session straight from the conversation: Devin writes the prompt from what it learned, and the session's status shows inline. Exploring here is far cheaper than letting a full session discover the same things by trial and error, so it's the right place to remove ambiguity.

CHAPTER 03

## Anatomy of a session

A session is a disposable laptop with an engineer attached. Knowing what's on that laptop, and how to step in, makes you a much better collaborator.

User

### Lifecycle

Nothing a session installs persists. If Devin keeps re-installing the same thing, that belongs in the blueprint (chapter 09).

### The workspace

| Tool | What it is | When you use it |
| --- | --- | --- |
| Progress tab | Unified timeline of every command, edit and browser action. | Click any step to see exactly what Devin did and why. |
| Shell | Devin's terminal with full command history and output previews. | See what Devin tried. Greyed commands are later in time; click to jump. |
| IDE | VS Code with your repos, Cmd/Ctrl+K, Cmd/Ctrl+I, tab complete. | Review diffs live; take over for a fix. Pause Devin first, then tell it what you changed. |
| Browser / Computer | Interactive browser or full desktop (with Computer Use). | Test UI, complete MFA or CAPTCHA, log in. Ask Devin to "save the browser profile" to persist logins into the blueprint. |
| Side chats | Read-only Q&A next to the main thread. Start with `/btw your question`. | Ask "why did you do that?" without derailing the work. |
| Session Insights | Post-session analysis: size, issues, improved prompt, knowledge usage. | After any L/XL session or anything that went sideways. |

### Where sessions start

| Entry point | How | Best for |
| --- | --- | --- |
| Web app | Home prompt box, or from Ask Devin | Planned work with attachments, playbooks, repo selection |
| Slack / Teams | Tag `@Devin` in a thread | Bugs discussed with coworkers; Devin replies in-thread |
| GitHub PR | Comment `/devin <prompt>` or `/devin review` | Fixing a failing test or nit on an open PR |
| Jira / Linear | Assign or label a ticket | Ticket-driven work, with confidence scoring |
| Devin CLI | `devin` then `/handoff` | Start locally, send long work to the cloud |
| Automations / API | Triggers, webhooks, schedules, Devin MCP | Recurring or event-driven work |

### Modes and models

Cloud sessions and the local agent pick models differently. In the web app, the session start box has an agent or capability picker. Current tiers include a cheaper **Lite** mode, **Normal**, and a heavyweight **Ultra**, with research previews appearing over time. Exact model names change often; check the picker. Match the mode to the task: Devin Coach will flag Ultra on a one-line change.

Devin CLI and Devin Desktop (local sessions) have their own model picker with three kinds of choice. Cognition now recommends **Fusion** for most users.

| Choice | What it does | Select with | Best for |
| --- | --- | --- | --- |
| Fusion recommended | A frontier **lead** model plans, reasons and reviews; a cheaper **sidekick** writes the code, runs builds and tests. You talk to one Devin. | CLI: `/fusion` or `/model fusion`. Desktop: model selector or `/model` | Complex work where you want frontier quality without frontier prices on every token |
| Adaptive | A router that sends each request to a light or heavy model based on the prompt. | `/model adaptive` or `--model adaptive` | Mixed day-to-day work; cheapest default on self-serve |
| A single model | One model for everything. Short names (`opus`, `sonnet`, `swe`, `codex`, `gemini`) resolve to the latest in that family. | `/model opus`, `--model swe` | A specific model's strengths; quick cheap edits on `swe` |

#### Fusion: how the pairing works

Most tokens in a typical session are mechanical implementation, so moving them to the sidekick is where the savings come from. Cognition's recommended starting pairing is **Fable 5.1 lead + SWE-2 sidekick**; Fusion's internal instructions are tuned per pairing.

- **Where it runs:** Devin CLI **3000.10.20+** and Devin Desktop **3.10.0+**, in local sessions. `/fusion` is CLI-only; in Desktop use the model selector. Switch back to a single model at any time with `/model`.
- **Who gets it:** paid plans only. Not on free or trial tiers, and **not** on legacy or credit-based plans, including enterprises still on legacy credits billing.
- **Billing:** the lead bills at its frontier rate and the sidekick at its lower rate; both rates show in the picker. Self-serve draws down quota per token; Cognition-platform enterprises are metered in ACUs that scale with both models' tokens.
- **Measure it:** run `/session-stats` (or `/stats`) in the CLI to see tokens, cost by model and estimated Fusion savings. Compare total cost and result quality on similar tasks, not token price alone.

**When to reach for which**

Fusion for multi-file features, refactors and investigations you'd otherwise give a frontier model. A single `swe` model for small, obvious edits. Adaptive when you don't want to think about it. For long work, start locally on Fusion to plan, then `/handoff` to a cloud session.

### Session sizing

Session Insights classifies every session by ACU consumption. L and XL are flagged unhealthy: the task was too broad, the prompt too vague, or the environment broken.

| Size | Self-serve ACUs | Enterprise ACUs (×10) | Read it as |
| --- | --- | --- | --- |
| XS | ≤ 2 | ≤ 20 | Quick fix or answer |
| S | ≤ 5 | ≤ 50 | Well-scoped ticket |
| M | ≤ 10 | ≤ 100 | Healthy upper bound for one session |
| L | ≤ 20 | ≤ 200 | Unhealthy: split or fix the environment |
| XL | > 20 | > 200 | Unhealthy: investigate with Insights |

CHAPTER 04

## Prompting well

Treat a prompt like a ticket you'd hand a strong new hire: scope, pointers, decisions already made, and a definition of done.

User

### The four ingredients

| Ingredient | Why it matters | Example phrase |
| --- | --- | --- |
| Context | Which repo, which area, what for. | "In `payments-api`, the refund endpoint…" |
| Steps or decisions | Removes forks in the road where Devin could guess wrong. | "Use a composite index, not a cache." |
| Patterns to follow | Devin copies what you point at. | "Follow `handlers/charge.py` for error handling." |
| Success criteria | Lets Devin verify itself and know when to stop. | "Done when `pixi run ci` passes and the new test covers the 409 path." |

#### Vague

> Improve our database performance.

Open-ended, no target, no way to verify. Expect an L or XL session and a PR you don't want.

#### Specific

> In orders-service, optimize getOrderDetails in orderService.js: add a composite index on order_items(order_id, product_id) via a new migration, and replace the correlated subquery with a JOIN to products. Keep the response shape identical. Done when existing tests pass and a new test asserts the query plan uses the index.

Scoped, decided, verifiable.

### Pre-flight checklist

- **Clear start and end.** Can you state success in one sentence that a test, CI run or script can check?
- **Size.** Under about three hours of human work? If not, split it, or ask for managed Devins.
- **Pointers.** Files to imitate, doc links, Figma via MCP, example inputs and outputs.
- **Tools connected.** Does Devin need Sentry, Datadog, a database? Connect the MCP first.
- **Self-test instruction.** "Run the app and screenshot `/settings` at 1440px and 375px." Devin has a real browser.
- **One task per session.** Unrelated follow-ups belong in a new session. Devin Coach will say so.

### Reusable shortcuts

| Syntax | What it does | Defined by |
| --- | --- | --- |
| `@skills:name args` | Invokes a skill; args fill `$ARGUMENTS`, `$1`…`$9`. | Repo SKILL.md files or plugins |
| `!macro` | Attaches a playbook (blue pill appears; editable before send). | Settings → Playbooks |
| `/command` | Expands an org slash command into its prompt template. | Org admins, Settings → Devin → Commands |
| `/btw question` | Opens a read-only side chat. | Built in |
| `/devin …` on a PR | Starts a session on that PR, or `/devin review`. | GitHub integration |

CHAPTER 05

## The context stack

This is the core of molding Devin to your team. Each mechanism loads at a different moment and costs a different amount of attention. Pick by when the guidance is needed.

UserAdmin

Every piece of guidance has a cost: context loaded into every session dilutes attention for everyone. Cognition's own advice for the CLI is direct: use skills wherever possible, keep rules and AGENTS.md as small as possible, and use a rule to point at skills. The diagram shows when each mechanism enters the session.

### The mechanisms side by side

| Mechanism | Lives in | Loaded | Versioned | Scope | Use it for |
| --- | --- | --- | --- | --- | --- |
| AGENTS.md | Repo (root or subdir) | Always, first 16 KiB | Git | Repo | Non-negotiables, setup commands, pointers to skills |
| Rules | `.devin/rules/`, Customize → Rules, plugins | Always, or by trigger | Git or plugin | Repo, personal, org, enterprise | Standing conventions that apply broadly |
| Skills | `.agents/skills/` or plugins | On demand | Git or plugin | Repo, personal, org, enterprise | Procedures: test, deploy, scaffold, investigate |
| Playbooks | Web app | When attached | Version history in app | Org, enterprise, system | Whole-task templates across repos |
| Blueprint knowledge | Blueprint YAML | Session start, per repo | UI or git-backed | Repo | Exact lint, test, build commands |
| REVIEW.md | Repo, any dir | During Devin Review | Git | Directory subtree | What reviewers must check or ignore |
| DEVIN_PR_TEMPLATE.md | Repo | When opening a PR | Git | Repo | Devin-specific PR body without changing the human template |
| Knowledge | Web app (legacy) | Trigger match or pinned | None | Repo pin, org, enterprise | Nothing new. Migrating to skills |

**Knowledge is deprecated**

Cognition is migrating every Knowledge note into a skill inside a **Knowledge plugin** at the same scope (enterprise, org or personal). Name becomes the skill name, trigger becomes the description, content is copied verbatim, folders and repo pins move into metadata, and migrated skills are marked `user-invocable: false`. Behavior doesn't change and no action is required. Edit migrated items under **Customize → Skills**. API users should move from `/v3/.../knowledge/notes` to the `/v3beta1/.../managed-plugins` skill endpoints.

### It reads your existing agent files

You probably don't start from zero. Devin picks up guidance already written for other tools, so a repo that serves Claude Code or Cursor today already shapes Devin.

| Already have | Who reads it |
| --- | --- |
| `AGENTS.md`, `AGENT.md`, `AGENTS.local.md` | Cloud sessions, CLI, Devin Review |
| `CLAUDE.md`, `~/.claude/CLAUDE.md`, `.claude/skills/` | CLI reads CLAUDE.md as rules; skills scanned in `.claude/skills/`; Review reads CLAUDE.md |
| `.cursor/rules/*.mdc`, `.cursorrules` | CLI rules (honours globs / alwaysApply); Review |
| `.windsurf/rules/`, `.windsurfrules` | CLI rules (`.devin/` wins on conflict); Review |
| `CONTRIBUTING.md`, `.coderabbit.yaml`, `greptile.json` | Devin Review |

Control CLI imports with `read_config_from` in `.devin/config.json` if you want Devin to ignore a legacy format.

#### Where should this guidance go?

Answer three questions about a piece of guidance you want Devin to follow.

What kind of guidance is it?

A standing convention ("always…", "never…") A step-by-step procedure A whole-task template I start sessions with The exact lint / test / build command Something reviewers should check

How often does it matter?

In nearly every session Only for certain tasks or files

Who does it apply to?

One repository A team across many repos The whole company Just me

CHAPTER 06

## Skills, rules and playbooks in depth

Skills are now the main unit of teaching. Learn the file format well; it's an open standard and also works in other agents.

UserAdmin

### Skills

A skill is a `SKILL.md` file following the open [Agent Skills spec](https://agentskills.io/specification). At session start Devin sees only each skill's name and description. When a skill is invoked, its body is injected as a system-level instruction and Devin follows the steps in order.

#### Where Devin finds them

All six paths are scanned in every repo: `.agents/skills/` (recommended), `.devin/skills/`, `.github/skills/`, `.claude/skills/`, `.cognition/skills/`, `.windsurf/skills/`, each as `<skill-name>/SKILL.md`. Skills from indexed repos are available before cloning; once a repo is cloned, the on-disk version from the working branch wins. Plugin skills appear as `/<plugin>:<skill>`.

#### Frontmatter reference

| Field | Purpose | Tip |
| --- | --- | --- |
| `name` | Identifier; defaults to the folder name. | kebab-case, matches the folder. |
| `description` | What Devin reads to decide relevance. | Write it as a trigger: "Use when…". This is the most important line in the file. |
| `allowed-tools` | Restricts tools while active. | `Read, Grep, ListDir` for safe read-only investigation. |
| `argument-hint` | Shown next to the name. | `<environment>` |
| `triggers` | Who may invoke. Default `["user", "model"]`. | `["user"]` for deploys and anything with side effects. |

The body supports `$ARGUMENTS` and `$1`–`$9`, plus `` !`command` `` blocks that inject live output (branch name, last commit) when invoked. A skill can also ship a `workflow.py` so a multi-agent workflow becomes reusable.

**One active skill**

Only one skill can be active at a time; invoking another replaces it. Don't design skills that depend on composing. Make each one self-contained, and let a short rule say which skill to use when.

**Let Devin write them**

After Devin figures out how to run or test your app, it proposes a skill in the timeline with a **Create PR** button. Approve the good ones. Over a few weeks this builds a library of "how to run, test, deploy" skills grounded in what actually worked.

### Rules

Rules are standing guidance. In the web app they live on **Customize → Rules** at personal, org or enterprise scope; in repos they live in `.devin/rules/*.md` (one per file) or `.devin/global_rules.md`. Rule frontmatter supports `trigger` values `always_on`, `manual`, `model_decision`, `agent` and `glob`.

.devin/rules/api-handlers.md

```
---
description: "HTTP handler conventions"
trigger: glob
globs: "services/**/handlers/*.py"
---
- Validate input with the pydantic models in schemas/; never parse raw dicts.
- Return errors via errors.problem() (RFC 9457). Never raise bare HTTPException.
- Every handler gets a test in tests/unit/handlers/ named test_<handler>.py.
```

### Playbooks

A playbook is a custom system prompt for a repeated task, stored in the web app with version history. Attach it when starting a session (or type its `!macro`); you can edit it inline before sending. Scopes: Organization, Enterprise, and Cognition-maintained System playbooks.

#### The playbook skeleton

| Section | Contains |
| --- | --- |
| Overview | The outcome in one or two sentences. |
| Required from user | Inputs Devin can't get itself: ticket, dataset, token. |
| Procedure | One imperative step per line, setup → work → delivery. Mutually exclusive, collectively exhaustive. |
| Specifications | Postconditions: what's true when done. |
| Advice | Corrections to Devin's priors. Step-specific advice goes as a sub-bullet under that step. |
| Forbidden actions | Hard lines: "Do not modify migrations already merged to main." |

Playbook · !dep-bump

```
## Overview
Upgrade one third-party dependency to the requested version and leave the repo green.

## Required from user
- Repository, package name and target version

## Procedure
1. Read the package's changelog between the current and target versions; list breaking changes.
2. Update the version constraint in pixi.toml (conda-forge first) and run `pixi install`.
3. Fix call sites affected by each breaking change.
   - Search with ripgrep for every symbol named in the changelog.
4. Run `pixi run ci` and iterate until it exits 0.
5. Open a PR titled `chore(deps): bump <pkg> to <version>` with the breaking-change list in the body.

## Specifications
- `pixi run ci` passes; coverage did not drop.
- pixi.lock is updated in the same commit.

## Advice
- Prefer the package's documented migration path over shims.

## Forbidden actions
- Do not pin unrelated packages.
- Do not use pip or uv.
```

### Skill or playbook?

|  | Skills | Playbooks |
| --- | --- | --- |
| Lives in | Repo (git) or plugin | Devin web app |
| Triggered | Automatically by description, or `@skills:` | Manually attached at session start |
| Reaches | Cloud, CLI, Desktop, other Agent Skills tools | Cloud sessions |
| Created by | You, or suggested by Devin | You, or Devin from past sessions on request |
| Best for | Repo-specific procedures; reusable steps inside many tasks | Whole-task templates that span repos or teams |

CHAPTER 07

## Plugins and governance

Plugins are how an admin ships context to every engineer at once, across cloud, CLI and Desktop, and how you control what's allowed.

Admin

A plugin is a directory with a `.devin-plugin/plugin.json` manifest. Everything else is optional.

plugin layout

```
my-plugin/
├── .devin-plugin/plugin.json   # name, version, required / optional / forbidden lists
├── AGENTS.md                   # always-on rule; keep it short
├── rules/                      # triggered rules
├── skills/<name>/SKILL.md      # exposed as /my-plugin:<name>
├── hooks.json                  # lifecycle hooks (best effort)
├── .mcp.json                   # MCP servers; secrets as ${NAME}
└── agents/<name>.md            # subagents (CLI / Desktop today)
```

### The Customize page

**Customize** in the sidebar has five tabs: Plugins, Skills, MCPs, Hooks and Rules, each shown per scope (Personal, Organization, Enterprise) as the *effective* result after governance. Install from the official marketplace (`CognitionAI/devin-marketplace`), from a git repo, from a `.zip`, or create one in the built-in editor. Changes apply to the **next** session; running sessions keep what they loaded.

### Scopes and precedence

### The recommended pattern: a team marketplace with a meta-plugin

1. Fork [team-marketplace-template](https://github.com/CognitionAI/team-marketplace-template). Keep all your plugins in one private repo under `plugins/<name>/`.
2. Make the repo root a **meta-plugin** whose manifest lists your baseline in `requiredPlugins`.
3. Validate in CI with the bundled `node scripts/validate-template.mjs`; test locally with `devin plugins install --local ./plugins/x`.
4. An admin installs the repo once at enterprise or org scope from **Customize → Plugins → Add plugin → From repository**.
5. Pin by SHA when you want change control; otherwise merging to the tracked branch reaches the next session automatically.

.devin-plugin/plugin.json (repo root, meta-plugin)

```
{
  "name": "acme-engineering-baseline",
  "requiredPlugins": [
    { "source": "git-subdir", "url": "https://github.com/acme/devin-plugins.git", "path": "plugins/python-standards" },
    { "source": "git-subdir", "url": "https://github.com/acme/devin-plugins.git", "path": "plugins/security-guardrails" }
  ],
  "optionalPlugins": [
    { "source": "git-subdir", "url": "https://github.com/acme/devin-plugins.git", "path": "plugins/frontend-standards" }
  ],
  "forbiddenPlugins": ["untrusted-vendor/*"]
}
```

**Lockdown mode**

To allow only approved plugins, forbid `"*"` in the enterprise manifest and list approved plugins in required or optional. Only *directly listed* entries are exempt from the wildcard, so also list every transitive dependency the meta-plugin pulls in.

### Plugin MCPs and secrets

- For OAuth MCPs at org or enterprise scope, **Access** decides sharing: **Organization** is one shared connection (use a service account), **Personal** means each member authorizes their own.
- Never put secrets in plugin files. Reference them in the manifest `env` as `secret:org:NAME`, `secret:enterprise:NAME`, `secret:personal:NAME` or `secret:repo:owner/repo:NAME`.
- Indexing issues ("Plugin repos unauthorized", "No plugin manifest found", pin conflicts) appear under **Plugin settings**. Granting repo access to the org is the usual fix.

CHAPTER 08

## Worked example: encoding a house style

The same team standard, split across the right mechanisms. Adapt the content; keep the split.

UserAdmin

Take a typical Python standard: pixi as the only runner, ruff and strict mypy, structlog instead of `print`, at least 90% coverage, Conventional Commits, and a single `pixi run ci` gate. Here is where each piece goes.

Enterprise plugin

**python-standards**

Rule (always-on, short) + skills used everywhere"pixi only, structlog only, Conventional Commits" + `/python-standards:pr-ready`

Repo

**AGENTS.md**

Repo-specific non-negotiables and pointersPackage layout, service names, which skills to use when

Repo

**.agents/skills/**

Procedures that only make sense here`run-integration-tests` (needs Postgres), `add-endpoint`

Blueprint

**knowledge**

The exact commands`pixi run test`, `pixi run lint`, `pixi run ci`

Repo

**REVIEW.md**

What reviewers must enforceNo `print()`, no bare `except`, typed public APIs

Repo

**DEVIN_PR_TEMPLATE.md**

PR body Devin fills inSummary, test evidence, CI output, risk

AGENTS.md

```
# AGENTS.md

## Non-negotiables
- Run everything through pixi: `pixi run test`, `pixi run lint`, `pixi run check`. Never pip, uv, or bare python.
- Python 3.12+ syntax (`X | Y`, `list[str]`), full type hints, mypy strict.
- Logging: structlog only. No print(), no stdlib logging.
- Never `except:` bare; never swallow exceptions.
- Branches: feature/, bugfix/, hotfix/. Commits: Conventional Commits.

## Definition of done
`pixi run ci` exits 0 (pre-commit, build, mypy, ruff, pytest with --cov-fail-under=90).
Every change ships with a test in tests/unit/ or tests/integration/.

## Layout
- src/<package>/ (src layout); tests mirror it under tests/unit and tests/integration.

## Which skill to use
- Before opening any PR: @skills:pr-ready
- Integration tests needing Postgres: @skills:run-integration-tests
```

.agents/skills/pr-ready/SKILL.md

```
---
name: pr-ready
description: Use before opening or updating any pull request in this repo. Runs the full CI gate and fixes failures.
---

## Gate
1. Run `pixi run fmt`, then stage any files it changed.
2. Run `pixi run ci`.
3. If it fails, read the full output, identify the failing step and first error line, apply one focused fix, and re-run from step 1.
4. If the same error appears 10 times in a row, stop. Summarise the failing step, the exact error, and every attempt in the PR description under "Blocked", and open the PR as draft.

## PR
1. Title: Conventional Commit matching the branch prefix (feature/ → feat:, bugfix/ → fix:).
2. Body: fill DEVIN_PR_TEMPLATE.md, paste the tail of the passing `pixi run ci` output.

## Current context
- Branch: !`git branch --show-current`
- Changed files: !`git diff --name-only origin/main...HEAD`
```

REVIEW.md

```
# Review guidelines

## Critical areas
- src/*/auth/ and anything touching credentials: check for secrets in code or logs.
- Database migrations must be backward compatible with the previous release.

## Conventions
- Flag any print() or `import logging`; structlog is required.
- Flag bare `except:` and `except X: pass`.
- Public functions need type hints and Google-style docstrings.

## Ignore
- pixi.lock unless pixi.toml also changed.
- Generated files under src/*/_generated/.
```

**Start with what you have**

If your repos already carry a CLAUDE.md or Cursor rules, Devin and Devin Review already read them. Migrate gradually: move procedures into skills, shrink the always-on file, and add REVIEW.md where review needs differ from authoring rules.

CHAPTER 09

## The environment: blueprints, builds, snapshots

Cognition calls environment configuration the single highest-leverage thing you can do. A Devin that can't run your tests can't check its own work.

UserAdmin

| Concept | What it is | Docker analogy |
| --- | --- | --- |
| Blueprint | YAML: tools to install, deps to keep current, commands to know | Dockerfile |
| Build | Runs blueprints, clones repos, produces a snapshot | `docker build` |
| Snapshot | Frozen bootable VM image. One active per org. | Image |

### Build order

### Blueprint sections

| Section | Runs | Put here |
| --- | --- | --- |
| `initialize` | Builds only; saved in snapshot | Runtimes, system packages, global CLIs |
| `maintenance` | Builds; shown to Devin at session start (not auto-run) | Fast, incremental dependency installs; credential config files |
| `knowledge` | Never executed; loaded into context for that repo | Exact lint, test, build commands |
| `post-build` | Org/enterprise only, after everything | Assertions on the assembled machine |
| `clone` | Repo only, at clone | Non-default branch, path, skip submodules or LFS |

Repository blueprint (pixi project)

```
initialize: |
  curl -fsSL https://pixi.sh/install.sh | bash

maintenance: |
  # incremental and non-interactive; Devin may re-run this after pulling
  ~/.pixi/bin/pixi install

knowledge:
  - name: test
    contents: pixi run test
  - name: lint
    contents: pixi run lint && pixi run check
  - name: full gate
    contents: pixi run ci   # must exit 0 before any PR
```

Check PATH handling against Cognition's template library (the `$ENVRC` pattern) before relying on this verbatim.

### Where configuration goes (enterprise)

| Tier | Rule of thumb | Examples |
| --- | --- | --- |
| Enterprise | Default for anything shared | Language runtimes, corporate CA certs, proxy config, internal CLIs, shared registry auth, scanners |
| Organization | Only for things that must *not* reach every org | A team-only private registry |
| Repository | Anything that runs in the repo directory | `pixi install`, `npm install`, repo knowledge |

**Two classic mistakes**

Repo commands like `npm install` in an enterprise or org blueprint run in `~`, not the repo, and fail. A secret written to a config file during `initialize` persists in the snapshot; write credentials in `maintenance`, which reruns every build.

### Operating it

- **Fastest start:** open a session and say "Set up your environment for this repo." Approve the suggestion cards; a build runs.
- **Build statuses:** Success, Partial (some repo steps failed but snapshot usable), Failed (org/enterprise step, clone, or health check failed), Cancelled.
- **Pinning:** lock to a Success or Partial build under 7 days old. Pins never expire, so unpin after the release or debug window.
- **Git-backed:** store `.devin/blueprint.yaml` in the repo and sync via API or UI after merge, so blueprint changes go through code review.
- **Browser logins:** log in once in the session browser, ask Devin to "save the browser profile", approve the blueprint change.
- **Other platforms:** Windows sessions (\~9% more usage), macOS sessions with iOS Simulator, Android emulator, VPN, and self-hosted **Outposts**.

CHAPTER 10

## Devin Review and closing the loop

When agents write the code, review becomes the bottleneck. Devin Review plus Auto-Fix lets PRs arrive close to merge-ready.

UserAdmin

Devin Review reorganizes a PR into logically grouped diffs with explanations, detects moved code, runs a **bug catcher** (severe and non-severe bugs, investigate and informational flags), and runs a **security scan** on every review (injection, auth flaws, secrets exposure, SSRF, weak crypto and more, with CWE IDs). You can chat about the PR and have the chat agent commit fixes.

### Ways in

- [app.devin.ai/review](https://app.devin.ai/review) lists PRs assigned to you, authored by you, and awaiting your review.
- Comment `/devin review` on a PR in a connected repo.
- Swap `github.com` for `devinreview.com` in any GitHub.com PR URL.
- Paste a GitHub Enterprise, GitLab or Azure DevOps PR URL into the review page. Bitbucket isn't supported.

### Auto-review trigger modes

| Mode | Runs on | Set by |
| --- | --- | --- |
| Auto review | PR opened, new commits, draft marked ready | Repo (admin) or user |
| On PR creation | PR opened or marked ready only | Repo (admin) or user |
| Manual | Only when you click | User only |

When a repo and a user enrollment both match, the most permissive mode wins. Any user with a connected GitHub account can self-enroll in **Settings → Preferences**.

### Teaching the reviewer

Devin Review reads `**/REVIEW.md`, `**/AGENTS.md`, `**/CLAUDE.md`, `**/CONTRIBUTING.md`, `.cursorrules`, `.windsurfrules`, `.cursor/rules`, `*.rules`, `*.mdc`, `.coderabbit.yaml` and `greptile.json`. Files are scoped to their directory, so `src/payments/REVIEW.md` applies only under `src/payments/`. Admins can add extra globs under **Settings → Review → Review Rules**, for example `docs/adr/**/*.md`.

### Auto-Fix

With Auto-Fix on, Devin responds to review findings and CI failures on its own PRs. Enable it from the review sidebar on a Devin PR, or under **Settings → Devin → Pull requests → Responding to bots** by allow-listing `devin-ai-integration[bot]`. Org admin only.

### Admin controls

| Control | Where | Why |
| --- | --- | --- |
| Repos auto-reviewed | Settings → Review → Repositories | Review every PR on critical repos |
| What gets posted | Settings → Review → Post as PR comments | Bugs and security on by default; flags off. Optional CI status check. |
| Per-PR auto-review spend limit | Settings → Review → Auto-review limits | Stops high-churn PRs eating ACUs; manual reviews still work |
| Who gets which tier | Custom roles (manual / on-creation / full / manage) | Roll out by group |
| Consumption | Settings → Consumption (Review line, per user, per repo) | See cost and bugs caught per repo |

**Process stays human**

Devin's PRs are subject to the same branch protections as anyone's. Configure required reviewers so a human approves every Devin PR before merge. Devin never comments or commits as a user without that user initiating it.

CHAPTER 11

## Parallel work: managed Devins and workflows

Devin's biggest returns come from many small, verifiable slices run at once, not from one heroic session.

User

#### Tall and deep

Complex net-new features, even repetitive ones. Lower reliability at scale; each run needs judgment.

#### Wide and shallow

Many simple, isolated, verifiable slices: one file, module or notebook each, under \~90 minutes of human work, backwards compatible, independently mergeable. Highly reliable.

### Three ways to parallelize

| Approach | How | Use when |
| --- | --- | --- |
| Several plain sessions | Start them yourself, or via API | Two or three unrelated tasks |
| Managed Devins | "Spin up a managed Devin per module using the !test-coverage playbook" | A coordinator should scope, launch, monitor, resolve conflicts and compile results |
| Dynamic workflow | "Use a workflow to…"; Devin writes a deterministic Python script with `agent()`, `pipeline()`, `parallel()` | Five or more units with a combine step, or staged audit → fix → verify pipelines. Resumable for up to 7 days. |

**Cost**

Every child agent is a full session. Run a workflow on a slice (one directory, three modules) first and check ACUs in the workflow panel. Use a cheaper mode for high-volume classification stages. Enterprise admins must switch on **Dynamic workflows** in Enterprise Settings → Devin before anyone can use them.

Once a workflow works, ask Devin to save it as a skill (`workflow.py` next to `SKILL.md`) so the next run reuses the script instead of writing a new one.

CHAPTER 12

## Automations

Define the trigger once and Devin handles each event as it arrives. This is how Devin becomes part of the team's operating rhythm.

UserAdmin

### Starter automations worth building

| Name | Trigger | Prompt sketch |
| --- | --- | --- |
| Fix red CI | GitHub check run, `conclusion = failure` | "Read the failing job log, reproduce locally with `pixi run ci`, push a fix to the same branch. If it isn't caused by this PR, comment and stop." |
| Ticket to PR | Jira or Linear, label `devin` added | "Implement the ticket. Use @skills:pr-ready. Link the PR on the ticket." |
| Sentry sweep | Schedule, weekdays 08:00 | "Query Sentry MCP for new unresolved errors in the last 24h, group duplicates, open one PR per fixable root cause." |
| Bug triage | Triage Devin on `#bugs` | Template "Triage Bug Reports" with a setup prompt naming priorities and owners. |
| Incident first look | PagerDuty high-urgency triggered | "Pull Datadog traces around the alert window, identify the deploy or change most likely responsible, post findings in the incident channel." |
| Weekly hygiene | Schedule, Monday 09:00 | "Review new skill and rule suggestions, flag duplicates or conflicts, summarise for the platform team." |

### Details that matter

- **Preflight scripts** read `EVENT_FILE`, `STATE_FILE` (64 KiB, persists between runs) and `LAST_RUN_FILE`, and must write `{"run": true|false}` or `{"items": [...]}` to `OUTPUT_FILE`. Items fan out into one session each. **Run now** bypasses preflight.
- **Webhooks** authenticate with a secret shown once; send it in the `X-Webhook-Secret` header, not the query string. Payloads over 200 KB are truncated.
- **Public repos:** GitHub automations only fire on private repos unless an admin widens a connection's automation scope. Strangers can comment on public repos, so keep it that way unless you have a strong reason.
- **Untrusted input** (Slack, webhooks, tickets) should run under a restrictive network policy or security profile.
- **Schedules** are now a trigger type; the old Scheduled Sessions page is legacy. Times display locally and are stored in UTC.
- **Run as:** automations run as the Automations service unless a user has **Run Personal Automations**.
- **Infrastructure as code:** automations can be managed declaratively with a Terraform provider.

### Auto-triage

A persistent parent Devin watches one Slack channel, filters noise, deduplicates, and spawns child Devins that trace root cause, post a diagnosis in-thread and tag the code owner. Parent and children share a **scratchpad**: recently triaged items, a routing table of code areas to owners, and known duplicates. Correct a mis-route in the thread and it updates the table. Start with a dedicated bug channel, connect Sentry or Datadog MCPs, and set ACU and invocation limits.

CHAPTER 13

## Integrations and MCP

Three different mechanisms connect Devin to the outside world. Using the right one avoids credential sprawl.

Admin

| Mechanism | Gives Devin | Set up at | Examples |
| --- | --- | --- | --- |
| Native integrations | Repo access, session entry points, built-in workflows | Settings → Connections | GitHub (App, not PAT, for write features), GitLab, Bitbucket, Azure DevOps, Slack, Teams, Jira, Linear, PagerDuty |
| Plugins / MCP servers | Tools to query and act inside external services | Customize → Plugins, Customize → MCPs | Sentry, Datadog, Notion, Confluence, Figma, Postgres, Snowflake, Databricks, MongoDB |
| Secrets | Credentials for CLIs, scripts, browser logins | Settings → Secrets, blueprint Secrets tab | Registry tokens, service-account API keys, TOTP, site cookies |

### Secrets scopes

| Scope | Visible to | Notes |
| --- | --- | --- |
| Enterprise | All orgs | Shared registry and proxy credentials. Define once, high up. |
| Organization | All members' sessions; only admins can view or edit | Usable by every member, so scope accordingly |
| Repository | That repo's builds and sessions | Needs ManageOrgSecrets. Never written into the snapshot. |
| Personal | Your sessions only | Your own accounts and test credentials |
| Session | That session only | When Devin asks mid-session |

More specific beats less specific when names collide: repo over org over enterprise. Secrets aren't exported into every shell; Devin binds them to the commands that need them, so tell Devin which env var a script reads. Give Devin its own accounts (for example a dedicated `devin@` service user) and add a **note** to each secret describing where it may be used.

CHAPTER 14

## Admin: organizations and access

Org boundaries decide which repos Devin can see together, whose snapshot it boots, and where cost lands. Get them roughly right early.

Admin

### Structure

An enterprise contains organizations. Each org has its own repos (granted by enterprise admins), its own snapshot, its own members, and its own ACU limits. All org members can access all of that org's repos. Users can belong to several orgs. The **primary organization** holds enterprise-wide settings such as enterprise knowledge and Review settings.

Recommended mapping: one Devin org per GitHub team / IdP group

```
Enterprise
├── Payments Platform     ← GitHub team payments-team    · IdP product-payments
│   └── repos: payments-api, ledger, refunds-worker
├── Data Platform         ← GitHub team data-eng         · IdP eng-data
│   └── repos: pipelines, warehouse-models, airflow-dags
└── Platform & Security   ← GitHub team platform-infra   · IdP eng-platform
    └── repos: infra, deploy-scripts, security-tools
```

Decide with three questions: which teams collaborate on the same code, which repos must be visible together, and how you budget. A shared repo needed by two teams is a reason to put them in one org.

### Default roles

| Level | Role | Can |
| --- | --- | --- |
| Org | Admin | Everything in the org |
| Org | Member | Use Devin; manage day-to-day resources (knowledge, playbooks, secrets, snapshots); not membership or org settings |
| Org | DeepWiki Only | DeepWiki and Ask Devin, no sessions. Good for broad read-only rollout. |
| Enterprise | Admin | Everything across the enterprise |
| Enterprise | Member | Standard access in their orgs |

### Custom roles worth creating

Create them in **Enterprise Settings → Membership → Roles** (needs Manage Account Membership), then map IdP groups to them under **Groups (IdP)** so access follows your directory. One role per user per org, plus one account-level role.

| Role idea | Key permissions |
| --- | --- |
| Devin Champion (per org) | Use Sessions, Manage Playbooks, Manage Knowledge, Manage Repo Blueprints, Manage Schedules, Manage Automations, View Sessions, View Metrics |
| Environment Steward | Manage Org Blueprints, Manage Repo Blueprints, Manage Secrets, Index Repositories |
| Reviewer-only | Use DeepWiki, Use Ask Devin, Devin Review (manual) |
| Automation Owner | Manage Automations, Run Personal Automations, Manage MCP Servers |
| Auditor (account) | View Audit Logs, View Account Consumption, View Account Metrics, View Sessions |

Also on the admin list: SSO (Okta, Entra ID, SAML, OIDC), SCIM provisioning, IP access lists, customer-managed keys, service users for API automation, and audit logs.

CHAPTER 15

## Admin: security controls

Devin runs code, browses the web and holds credentials. Security profiles and guardrails let you set a floor no session can go below.

Admin

### Security profiles

A profile is a named bundle of up to four restrictions: a **network allowlist** (hostnames with `*`, CIDRs), an **MCP allowlist**, **Devin MCP read-only**, **git access** (read-only or full), and removal of the **GitHub CLI token**. Profiles exist at org and enterprise scope.

Profiles resolve when a session boots or wakes, so edits don't reach running sessions until they sleep and wake. Managing profiles needs the separate **Manage security profiles** permission.

**A sensible starting posture**

Enterprise default (recommended, not mandatory at first): network allowlist covering your git host, package registries, internal domains and docs sites. A stricter **mandatory** profile on the automations default for anything fed by Slack, webhooks or tickets. Read-only git for research-only automations.

### AI Guardrails

Enterprise-wide screening of user messages, follow-ups and PR comments for prompt injection, data exfiltration and policy violations. Each preset guardrail is Off, Log only, Warn user or Block message. Violations appear in a dashboard, in audit logs as `ai_guardrail_violation`, and via API. Start with Log only for two weeks, review the Violations tab, then move high-confidence guardrails to Warn or Block.

### Other controls

- **Plugin governance:** hide the official marketplace, restrict marketplace MCPs, forbid plugins by glob, or lock down to an allow-list (chapter 07).
- **Local agent controls:** enterprise policy for Devin CLI and Desktop, including disabling CLI plugins entirely. Under **Settings → Enterprise → Devin Desktop** you can allowlist models (include the Fusion family and its sidekicks if you want Fusion available), pin a team-wide default model, enforce terminal permissions and the sandbox, and gate web search and MCP servers. The allowlist always beats the default: a pinned default that isn't allowed falls back to the built-in one. A user's own `/model` choice persists for their later sessions.
- **Adaptive router:** disabled by default for enterprises; enable **Adaptive model router** under Settings → Devin Desktop → Models before users can pick it.
- **Attribution filtering** and the **Trust Center** for compliance reviews.
- **Devin Coach blockers:** recurring runtime blockers (blocked network, missing permissions) show which single fix would unblock the most sessions.

CHAPTER 16

## Admin: usage and cost

Cost tracks the quality of how Devin is used. The same habits that cut ACUs also improve results.

Admin

### What consumes ACUs

Actions Devin takes (planning, context gathering, execution, browsing) plus a small amount of VM time and bandwidth. Little or nothing is consumed while Devin waits for you, waits on a test suite, clones repos, or sleeps. Drivers: task complexity, prompt specificity, codebase size, files touched, runtime, conversation length and back-and-forth.

### The control stack

| Control | Caps | Notes |
| --- | --- | --- |
| Org ACU limit | An org's total | Use orgs as pooled budgets |
| Usage policies (beta) | Each member, monthly; local + cloud combined | Tiers by IdP group; resolved as direct assignment → highest-priority mapped tier → default tier |
| Per-member override | One person, temporary or permanent | Blast-radius preview before lowering limits |
| Approval policy | Requests for more | Manual, always approve to a ceiling, or approve based on efficiency score |
| Automation limits | Per-session ACUs and invocations/hour | Set on every automation |
| Review spend limit | Auto-review ACUs per PR | Review is excluded from per-user limits but counts toward org limits |
| Managed Devin limits | Per child session | The coordinator can set ACU limits on children |

### Model choice is a cost lever too

Usage policies combine local and cloud usage, so what people pick in Devin CLI and Desktop shows up in the same budget. **Fusion** is the main lever: routine implementation tokens move to the cheaper sidekick while the frontier lead keeps the hard reasoning. On the Cognition platform both models' tokens are metered in ACUs at their own rates. Two admin facts to know before you recommend it:

- Fusion isn't available on **legacy credits billing**. If your enterprise is still on credits, it won't appear in the picker until you move plans.
- Make Fusion the team default only after a short bake-off: have champions run similar tasks on Fusion and on a single frontier model and compare `/session-stats` cost alongside PR quality.

Either the org limit or the per-user limit blocks new work when hit. Start tier setup with **Set up with recommendations**: it analyzes three billing cycles of per-member usage and simulates who would be blocked.

### Devin Coach

Checks prompts in the input box before they're sent and suggests fixes: plan first for big open-ended builds, switch off a heavyweight mode for small tasks, start a new session for unrelated follow-ups, split bundled tasks into child sessions, clear non-work prompts. Set **Prompt precheck** to Warning only (default), Warning and confirmation, or Off. Analytics show acceptance rates per suggestion type.

### Where to look

- **Settings → Consumption** (enterprise) and **Consumption Analytics** (org), with a Review line.
- **Session Insights** per session: size, issues, improved prompt.
- **My analytics** for individuals, including efficiency score (needs the View Personal Analytics permission and feature flag).

CHAPTER 17

## The improvement loop

Devin gets better at your codebase only if you turn each session's lessons into durable context. This loop is the SME's main job.

UserAdmin

### Reading the symptoms

| Symptom in Insights | Likely layer | Fix |
| --- | --- | --- |
| High ACUs, few user messages | Environment or scope | Check Action Items; add missing tools to the blueprint; split the task |
| Many messages, low ACUs | Prompt or context | Reuse the Improved Prompt; capture repeated corrections as a skill or rule |
| Misleading knowledge flagged | Context | Fix or delete the item; make its description narrower |
| Wrong category | Prompt | State the goal explicitly ("feature", not a bug description) |
| Same issue repeated in timeline | Environment | Missing tool, wrong version, permission error: fix once in the blueprint |
| Devin re-installs the same thing every session | Environment | Move it to `initialize` |
| Devin keeps asking how to run tests | Context | Blueprint `knowledge` entry plus a test skill |

### A weekly 30-minute routine for the SME

1. Open Consumption Analytics; list the week's L/XL sessions.
2. Open Session Insights on the top five. Note the layer each points at.
3. Approve or reject pending skill suggestions; merge the good PRs.
4. Check build health and Partial builds; unpin anything forgotten.
5. Skim Devin Coach blockers and guardrail violations.
6. Turn the best improved prompt into a playbook or skill.
7. Ask Devin to analyze a failed session: "This used 42 ACUs; I expected 12. Where did the time go, and what prompt would avoid it?"

CHAPTER 18

## A 90-day rollout plan

Tick items as you go. Progress is saved in this browser only.

UserAdmin

CHAPTER 19

## Pitfalls and how to avoid them

Most of these show up in the first month of any rollout.

UserAdmin

| Pitfall | Consequence | Do instead |
| --- | --- | --- |
| A 40 KB AGENTS.md | Only the first 16 KiB is auto-loaded; key rules silently missed | Keep under 16 KiB, critical items first, procedures in skills |
| Writing new Knowledge notes | Building on a deprecated feature | Write skills, in the repo or in an org plugin |
| Vague skill descriptions | Skills never auto-invoke, or fire at the wrong time | "Use when…" with concrete task and file cues |
| Deploy skill without `triggers: ["user"]` | Devin may run it on its own | User-only triggers for anything with side effects |
| Skipping environment setup | Devin can't run tests, so it can't verify; sessions balloon | Blueprint before broad rollout |
| Repo commands in enterprise blueprint | Run in `~`, fail or install in the wrong place | Repo blueprints for repo commands |
| Secrets in YAML or plugin files | Leak into logs, snapshots, git history | Secrets UI and `secret:` references |
| Forgotten snapshot pin | Stale environment indefinitely | Pin only for a defined window; calendar the unpin |
| One session, five tasks | L/XL session, tangled PR | One task per session; parallelize |
| Org-scoped OAuth MCP on a personal login | Everyone acts as that person | Service account, or Personal access |
| Automations on public repos or open Slack channels | Untrusted input drives sessions | Private repos, mandatory restrictive profile, guardrails |
| Running a frontier model alone for every local task | Paying frontier rates for mechanical edits and test runs | Fusion for complex work, `swe` for small edits; check `/session-stats` |
| Model allowlist that omits Fusion or its sidekick | Fusion missing from users' pickers; pinned default silently falls back | Allowlist the Fusion family and recommended sidekicks before setting it as default |
| Weakening branch protection for Devin PRs | Unreviewed code merges | Keep required human review; let Auto-Fix get PRs ready |

CHAPTER 20

## Self-check

Twelve questions you'll be asked as the resident expert.

UserAdmin

Built from Cognition's documentation at docs.devin.ai, read on 3 October 2026. Devin ships changes weekly; check the release notes before quoting limits or prices.

- [Skills](https://docs.devin.ai/product-guides/skills)
- [Knowledge (deprecation + migration)](https://docs.devin.ai/product-guides/knowledge)
- [Plugins and Customize](https://docs.devin.ai/product-guides/plugins)
- [Plugin ecosystem](https://docs.devin.ai/product-guides/plugin-ecosystem)
- [AGENTS.md](https://docs.devin.ai/onboard-devin/agents-md)
- [Rules](https://docs.devin.ai/cli/extensibility/rules)
- [Playbooks](https://docs.devin.ai/product-guides/creating-playbooks)
- [DeepWiki](https://docs.devin.ai/work-with-devin/deepwiki)
- [Ask Devin](https://docs.devin.ai/work-with-devin/ask-devin)
- [Blueprints](https://docs.devin.ai/onboard-devin/environment/blueprints)
- [Enterprise environment practices](https://docs.devin.ai/enterprise/environment-management/best-practices)
- [Devin Review](https://docs.devin.ai/work-with-devin/devin-review)
- [Automations](https://docs.devin.ai/product-guides/automations)
- [Dynamic workflows](https://docs.devin.ai/work-with-devin/dynamic-workflows)
- [Security profiles](https://docs.devin.ai/product-guides/security-profiles)
- [Custom roles](https://docs.devin.ai/enterprise/security-access/custom-roles)
- [Usage policies](https://docs.devin.ai/enterprise/features/usage-policies)
- [Session Insights](https://docs.devin.ai/product-guides/session-insights)
- [Fusion in Devin CLI](https://docs.devin.ai/cli/fusion)
- [Fusion in Devin Desktop](https://docs.devin.ai/desktop/fusion)
- [CLI models and Adaptive](https://docs.devin.ai/cli/models)
- [CLI team settings](https://docs.devin.ai/cli/enterprise/team-settings)
- [Release notes 2026](https://docs.devin.ai/release-notes/2026)
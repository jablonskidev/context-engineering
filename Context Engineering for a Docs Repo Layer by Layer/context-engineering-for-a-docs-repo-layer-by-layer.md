# Context Engineering for a Docs Repo, Layer by Layer

**Table of Contents:**
- [Why a Docs Repo Needs Context Engineering](#why-a-docs-repo-needs-context-engineering)
- [Layer 1: One Canonical Instruction File](#layer-1-one-canonical-instruction-file)
- [Layer 2: Context Files](#layer-2-context-files)
- [Layer 3: Deterministic Guardrails](#layer-3-deterministic-guardrails)
- [Layer 4: A Docs Skill That Routes Requests](#layer-4-a-docs-skill-that-routes-requests)
- [Layer 5: Skill Evals](#layer-5-skill-evals)
- [Layer 6: Writing Contracts](#layer-6-writing-contracts)
- [Layer 7: Evidence Rules](#layer-7-evidence-rules)
- [Layer 8: Integration Skills](#layer-8-integration-skills)
- [Layer 9: Multi-Stage Workflows With Saved Artifacts](#layer-9-multi-stage-workflows-with-saved-artifacts)
- [Layer 10: AI-Readable Published Docs](#layer-10-ai-readable-published-docs)
- [Layer 11: Design Tooling](#layer-11-design-tooling)
- [Layer 12: Drift Prevention](#layer-12-drift-prevention)
- [The Finished Repo](#the-finished-repo)
- [What to Build in Week One](#what-to-build-in-week-one)

## Why a Docs Repo Needs Context Engineering

If you drop an AI agent into a docs repo without giving it the right context, it will write pages that look correct if you squint but aren't really.
The agent will put pages in in the wrong section, make up product details, and write in a style that is all over the place.
Instead of saving time, you'll be stuck reviewing every sentence.

Context engineering means designing what an agent "knows," what it loads for each task, and what checks its work.
If you help your agents by giving them the right context, they can help you back by giving you outputs that are closer to deterministic.

In this article, you'll see how to set up context engineering for your docs repo in twelve layers.

## Layer 1: One Canonical Instruction File

**The problem:** An agent doesn't know your team's conventions unless it can read them.
If each agent tool gets its own copy of your rules, then the copies can drift apart, and then your agents will follow different rules.

**Why now:** Every subsequent layer points back to this file.

**Tools:**
- `AGENTS.md` is an open format for agent instructions. It's like a README for agents. Many agents read it automatically.
- Claude Code, GitHub Copilot, Gemini CLI, and Codex are AI coding agents. They read your repo, run commands, and propose or make edits.

**What to build:**
- An `AGENTS.md` file at the repo root with:
  - A map of the repo: where pages, generated files, and context files live
  - A rule for facts: back every statement about how the product works with a source you can check, such as the code, its tests, or generated reference docs, not the agent's memory
  - Safety rules: never expose credentials
  - The accessibility standard your pages must meet
  - Which checks to run after each kind of change, such as a prose lint after editing a page or a link check after moving one
  - Rules for script-built files, such as API reference pages built from comments in the code
  - Branch, commit, and pull request (PR) conventions
- A one-line pointer file for any tool that doesn't read `AGENTS.md` directly
- A PR template whose sections match the PR rules in `AGENTS.md`

**Example:** A minimal `AGENTS.md` looks like this:

```markdown
# Repository instructions

## Where things live
- Pages: `docs/`. Generated reference: `reference/` (don't hand-edit).
- Read PRODUCT.md, ARCHITECTURE.md, and STYLE.md before writing.

## Rules
- Ground every product claim in source code, tests, or generated reference.
- Never put credentials in pages, examples, or logs.

## Checks
- One page: `npm run lint -- <path>`. Links: `npm run linkcheck`.
```

**How you know it's working:** Ask two different agents which check to run after editing a page.
They should give the same answer.

**Pitfall:** Don't paste the full rules into each tool's own file.
Keep one copy and point to it.

## Layer 2: Context Files

**The problem:** Agents don't know who your readers are, how you organize your site, or how your team writes.

**Why now:** Your `AGENTS.md` already tells agents to read these files, so they need to exist.
Later layers also build on them. Layer 3 turns the style guide into automated checks, and Layer 6 turns it into page templates.

**What to build:**
- A product overview: audience, purpose, and positioning
- An information architecture guide: sections, page types, naming, and where new pages go. Tell agents how to read your structure, for example that a page groups related tasks and each task heading names one action.
- A style guide: voice, headings, code examples, and terminology

Keep these at the repo root.
Humans will read them too.

**Example:** The root of the repo gains three files:

```text
PRODUCT.md        # who reads the docs and what they're trying to do
ARCHITECTURE.md   # sections, page types, naming, where new pages go
STYLE.md          # voice, headings, code examples, terminology
```

**How you know it's working:** Ask an agent to add a page without saying where.
It should pick the right section and page type.

**Pitfall:** Don't write vague instruction, such as "Be clear."
Give the agent decisions it can apply, such as "Start each task heading with a verb."

## Layer 3: Deterministic Guardrails

**The problem:** Agents treat your style guide as guidance, not as rules they're forced to follow, so they apply it unevenly.
An agent saying it followed the rules isn't proof. You need checks that give the same answer every time.

**Why now:** Your `AGENTS.md` already tells agents which checks to run, and Layer 2 gave you written style rules to check against.
This layer builds those checks. Each later layer adds its own checks here, so build this foundation before you add more instructions for agents to follow.

**What to build:**
- Prose lint rules that encode your style guide
- A link checker
- Code examples that run in continuous integration (CI) on every PR

Start small.
Later layers add their own checks here.

**Tool:**
- Vale is an open-source prose linter.
  You write style rules in YAML, and it flags violations in your editor and in CI.

**Example:** This Vale rule flags em dashes:

```yaml
# .vale/styles/House/EmDashes.yml
extends: existence
message: "Use a comma, colon, or separate sentence instead of an em dash."
level: suggestion
nonword: true
tokens:
  - '—'
```

**How you know it's working:** Checks run on every PR, so reviewers can stop leaving comments the linter could have prevented.

**Pitfall:** Don't set a new rule to `error` before you've tested it.
An untested rule can block every PR and lead agents to rewrite correct text to satisfy it.
Start each rule at `suggestion`, and raise it only after its prove to be real problems.

## Layer 4: A Docs Skill That Routes Requests

**The problem:** Agents load `AGENTS.md` at the start of every session, no matter the task.
If you put every procedure there, then an agent fixing a typo will also read about your release-notes process and your page-review checklist.
Long instruction files fill the agent's context, which is the limited amount of text it can consider at once, and can make it follow your instructions less consistently.

**Why now:** Later layers will put their guidance inside this skill.
Build the skill first first so you won't have to move those files.

**Tools:**
- Agent Skills is an open format.
  A skill is a folder with a `SKILL.md` file that has a name, a description, and instructions.
  Agents load only the name and description at first and read the rest when a task matches.
  The folder location varies by agent, so check where yours looks.

**What to build:**
- A docs skill whose `SKILL.md` acts as a router: A route is the procedure for one kind of task, and the router picks one route per request and loads only that route's files.
- Two routes to start, write and review, that point to the context files
- More routes as later layers need them: plan, improve, maintain, ask, research, and release notes
- A rule that the agent must ask permission before switching to a route that edits files
- A rule that plans stay drafts until a person approves them
- An `AGENTS.md` line that tells every agent to load the skill for docs work

Users can name a route directly, using their agent's syntax for calling a skill, for example `/docs review <page>`.

**Example:** A starting `SKILL.md` looks like this:

```markdown
---
name: docs
description: Write or review pages in this docs repo. Use for /docs write and /docs review.
---

# Docs

Pick one route, then load only its file.

- write: follow routes/write.md
- review: follow routes/review.md
```

**How you know it's working:** Ask an agent to review a page.
Its file reads should show the review route and not the write route.

**Pitfall:** Don't build every route at once.
Add a route when you see a real need.

## Layer 5: Skill Evals

**The problem:** A skill is instructions, and a small wording change can break routing or behavior without anyone noticing.
An eval is a repeatable test of how an agent behaves.

**Why now:** The skill now exists, so its check should too.
Layer 3 checks the docs.
Evals check the agent.

**What to build:**
- A few routing cases: Does "review this page" reach the review route?
- Structural tests for the skill itself: links resolve and required files exist
- A record of which evals ran and which didn't

Every later layer adds cases here.

**Tools:**
- Waza is an open-source command-line tool from Microsoft for testing agent skills.
  It runs repeatable scenarios and grades the results with scripts or a rubric.

**Example:** A simplified eval case looks like this:

```yaml
prompt: "Review the install page for clarity."
expect:
  route: review
  edits_files: false
grader: rubrics/findings-ordered-by-severity.md
```

**How you know it's working:** When something breaks routing or behavior, a case fails.
You can break a route on purpose once to confirm your cases catch it.
Each eval run calls a model, so keep the checks proportional:
- Before you merge a skill change, you can run the cases that cover what you changed.
- When a result surprises you, you can rerun that case a few times, because agent output varies between runs.

**Pitfall:** Don't test only what the agent should do.
Agents often find something to fix even when nothing needs fixing.
Add cases where the right result is no change, such as a page that already meets your standards, and check that the agent leaves it alone.

## Layer 6: Writing Contracts

**The problem:** A style guide says how to write sentences.
It doesn't say what a troubleshooting page or a research report must contain.

**Why now:** The skill exists, so contracts go straight into its resource folders.
It's templates and checklists.

**What to build:**
- Content types for public pages, such as task guides, concept overviews, troubleshooting, and release notes
- Content types for internal artifacts: plans, research reports, and reviews
- Page blueprints, section templates, and review checklists
- Rules every page follows:
  - State prerequisites before the first step.
  - State the expected result.
  - Put output directly after the code that produces it.
  - Keep claims no stronger than the evidence justifies.

Add structural tests for these rules to Layer 3 and eval cases to Layer 5.

**Example:**

```text
skills/docs/
├── SKILL.md
├── routes/
├── types/        # task guide, concept, troubleshooting, plan, research report
├── blueprints/   # page shapes
├── templates/    # section templates
└── checklists/   # review checklists
```

**How you know it's working:** Review comments shift from structure ("where are the prerequisites?") to substance.

**Pitfall:** Don't write templates for agents to treat as checklists.
When an agent is given a template, it tends to fill out every section, so a short task page can end up with an empty troubleshooting heading or a "Prerequisites: none" line.
For each section in a template, say when to use it and when to leave it out.

## Layer 7: Evidence Rules

**The problem:** Your docs now follow your templates and pass their checks, but they can still be wrong.
When an agent doesn't know a fact, it often writes a plausible guess in the same confident tone it uses for verified facts.

**Why now:** Your pages have the right structure.
Now it's time to make the content true.
Until now, the only safeguard was one rule in `AGENTS.md`: `Back every product claim with the source code, its tests, or the API reference`.
Agents apply that rule unevenly, so this layer turns it into specific steps they can follow.

**What to build:**
- An order of trust for sources: code, tests, and generated reference before examples, tickets, and chat
- Confidence levels for every claim
- Local copies of the source code you document, kept out of version control
- A rule never to infer defaults, limits, or errors from examples
- One maintained record of feature facts, such as status, availability, and approved names. When the record is unavailable, work stops instead of guessing.
- A review rule: confirm each important claim from two independent sources
- Two read-only routes, ask and research. They return findings without changing files. To save findings or act on them, the agent asks permission to switch to a route that edits files.

**Example:**

```markdown
## Confidence
- High: code, tests, or generated reference say it directly.
- Medium: partial primary evidence.
- Low: inferred, stale, or conflicting. Mark it unverified and name the next check.
```

**How you know it's working:** When you ask where a claim came from, the agent points to a source you can check.
Its research and review results flag claims it couldn't verify instead of presenting them as fact.
Add eval cases for both.

**Pitfall:** Don't let the agent treat your existing docs as proof.
The page you're updating may be the one that's wrong, so checking a claim against it only repeats the error.
Use existing pages for context and terminology, but confirm any fact you add or change against the code, tests, or API reference.

## Layer 8: Integration Skills

**The problem:** Evidence lives in code changes, tickets, chat threads, and shared drafts.
Without access to them, the agent can't apply the evidence rules.

**Why now:** Layer 7 ranked your sources.
Now it's time to connect them.

**Tool:**
- Model Context Protocol (MCP) is an open standard for connecting AI tools to outside systems.
  An MCP server for a system lets the agent query it directly.

**What to build:**
- One small skill per system: GitHub for code changes, Jira for tickets, Slack for team discussion, and Google Docs for shared drafts
- In each skill, a rule to use the service's official MCP server when one exists, plus a small fallback script for queries it can't run
- A skill that maps which source repository is authoritative for which behavior

**Example:**

```markdown
## Jira
1. Use the Jira MCP server.
2. If it can't run the query, use scripts/jira.py.
3. Read the token from the JIRA_TOKEN environment variable. Never print it.
```

**How you know it's working:** The agent links each claim it took from a ticket or thread to that ticket or thread.
Add an eval case that checks for those links and confirms that no API tokens appear in the output.

**Pitfall:** Don't expose API tokens in skill files, logs, or agent output.
Keep tokens in environment variables.

## Layer 9: Multi-Stage Workflows With Saved Artifacts

**The problem:** "Write the release notes" is too big for one prompt.
When the agent does it in one pass, the research, the decisions, and the prose come back mixed together, and you can't check one without untangling the others.

**Why now:** You could chain stages earlier, but the results would be hard to trust.
A workflow becomes reliable once the integration skills from Layer 8 can gather sources, the evidence rules from Layer 7 decide what to trust, and the contracts from Layer 6 define what each stage's output must contain.

**What to build:**
- A workflow for release notes: generating an internal report on the changes in the release, reviewing and approving the report, and then generating release notes based on the report
- A docs maintenance workflow to keep up with product releases: using the release report, assess which pages the release affects, and then prioritize, plan, and apply the updates
- A saved artifact for each stage in a planning folder
- A rule that each stage waits for approval and never starts the next stage automatically
- A separate audience for each stage and file: the report is for the docs team, and the release notes are for new users
- Optionally, a bot that runs the first stage on a schedule and opens a PR with the report for a person to review. Later stages still wait for approval.

**Example:** The planning folder holds one artifact per stage:

```text
planning/
├── release-reports/<version>-report.md   # evidence and decisions, for a teammate
└── docs-updates/<version>/
    ├── assessment.md                    # which pages the release affects
    ├── priorities.md
    └── plan.md
```

**How you know it's working:** Over a few releases, most of your corrections happen in the report or the plan, and the final release notes need fewer edits.

**Pitfall:** Don't chain the stages automatically.
That will force the reviewer to check everything at once, which is the problem you started with.

## Layer 10: AI-Readable Published Docs

**The problem:** Your readers use agents too.
Agents that read your published HTML get navigation, scripts, and layout mixed into the content.

**Why now:** The same principles now apply to readers' agents. They need clean context, loaded on demand.

**What to build:**
- An `llms.txt` file and an `llms-full.txt` file with the full content, both generated at build time
- A Markdown version of every page, served at a predictable URL or when a request asks for Markdown
- A public skill file that tells outside agents how to search and cite your docs
- A listing on Context7

**Tools:**
- `llms.txt` is a proposed standard: a Markdown file at your site root that summarizes the site and links to its key pages for agents.
- Context7 is a service that indexes current documentation and serves it to coding agents.
- Astro is a web framework for content sites, and Starlight is its documentation theme. If your site uses them, generate these files in the same build.

**Example:** A generated `llms.txt` starts like this:

```markdown
# Example Docs

> Guides and reference for installing, configuring, and troubleshooting the product.

## Get started
- [Install](https://docs.example.com/install/index.md): Install and verify the CLI.
```

**How you know it's working:** Request a page's Markdown version and get clean text.
Ask an outside agent a question and it cites your pages.
Add a Layer 3 check that every page has a Markdown version and an `llms.txt` entry.

**Pitfall:** Don't maintain `llms.txt` by hand.
Every page you add, rename, or remove makes a hand-maintained file out of date, so have your build script create it instead.

## Layer 11: Design Tooling

**The problem:** Agents that edit the site's components and styles make generic design choices that don't match your site.

**Why now:** This work is independent of content, so it can come late.

**What to build:**
- A design-system file at the repo root: colors, type, components, and do's and don'ts
- A design skill and specialist subagents, separate agents that each handle one narrow job such as critique
- A hook: a command that runs automatically after the agent edits a file

**Tools:**
- Impeccable is an open design skill for coding agents.
  It gives agents design vocabulary and commands to critique, polish, and audit interfaces against your design system.

**Example:** A simplified hook configuration looks like this. The exact format depends on your agent:

```json
{
  "hooks": {
    "postToolUse": [
      { "matcher": "edit|create", "command": "node scripts/design-check.mjs" }
    ]
  }
}
```

**How you know it's working:** The hook flags a design problem on the same edit that introduced it.
Add an eval case with a known design fault and check that the agent fixes it.

**Pitfall:** Don't your design file fall out of sync with the live site.
Update the file whenever the live site changes, so it describes what you actually ship.

## Layer 12: Drift Prevention

**The problem:** Every layer changes over time.
Old instructions stick around, and agents follow whichever version they find first.

**Why now:** This layer maintains all the earlier ones, so it comes last.
Unlike the other layers, this one is never finished.

**What to build:**
- One source of rules, from Layer 1. It's your first defense.
- A rule to never hand-edit generated files: edit the source or the generator, regenerate, and review the diff
- Regular cleanup: retire superseded workflows, outdated model recommendations, and TODO sections
- A record of the evidence behind each skill change

**Example:** A generated-files rule in `AGENTS.md` looks like this:

```markdown
## Generated content
Don't edit files in `reference/`. Edit the docstrings or the generator,
run `npm run make-reference`, and review the diff.
```

**How you know it's working:** A new docs teammate can follow the README and `AGENTS.md` without hitting steps for scripts, files, or workflows that no longer exist.
Add a Layer 3 check that flags instruction files pointing to paths that don't exist, and an eval case where you ask the agent to fix a script-built file and it edits the source instead.

**Pitfall:** Don't document the new workflow next to the old one.

## The Finished Repo

After all 12 layers, the repo looks like this:

```text
.
├── AGENTS.md                     # Layer 1: rules for every agent
├── PRODUCT.md                    # Layer 2
├── ARCHITECTURE.md               # Layer 2
├── STYLE.md                      # Layer 2
├── DESIGN.md                     # Layer 11
├── .github/
│   ├── pull_request_template.md  # Layer 1
│   └── workflows/                # Layer 3: lint, links, and code examples on every PR
├── .vale/styles/House/           # Layer 3
├── scripts/                      # Layer 11: design check the hook runs
├── skills/
│   ├── docs/                     # Layers 4, 6, 7: router, routes, contracts, evidence rules
│   │   └── evals/                # Layer 5
│   ├── github/  jira/  slack/  google-docs/  source-map/   # Layer 8
│   └── design/                   # Layer 11
├── sources/                      # Layer 7: local copies of source code, not committed
├── planning/                     # Layer 9: saved workflow artifacts
├── tests/                        # Layers 3 and 6: structural tests
├── tools/                        # Layer 10: generators for llms.txt, llms-full.txt, and Markdown pages
├── public/skill.md               # Layer 10: skill file for outside agents
├── reference/                    # generated reference, never hand-edited
└── docs/                         # the pages
```

## What to Build in Week One

Start with the first three layers:
1. `AGENTS.md` with the repo map, safety rules, the evidence principle, and the checks to run
2. A product overview, an information architecture guide, and a style guide
3. A prose linter, a link checker, and code examples that run in CI

These three give every agent the same rules, a definition of good, and checks on its work.
Add the rest one layer at a time.

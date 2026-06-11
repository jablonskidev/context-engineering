# Building Context Infrastructure for AI-Assisted Docs

**Table of contents:**
- [Introduction](#Introduction)
- [Documentation Work Breaks When Context Stays Implicit](#Documentation-Work-Breaks-When-Context-Stays-Implicit)
- [What I Mean by Context Engineering](#What-I-Mean-by-Context-Engineering)
- [Why the Workflow Needed Structure](#Why-the-Workflow-Needed-Structure)
- [Reusable Output Was Still Too Slow to Trust](#Reusable-Output-Was-Still-Too-Slow-to-Trust)
- [A Correctly Blocked Artifact Is Still Useful](#A-Correctly-Blocked-Artifact-Is-Still-Useful)
- [Strong Stage Artifacts Can Still Create Workflow Friction](#Strong-Stage-Artifacts-Can-Still-Create-Workflow-Friction)
- [What the Pilots Taught Me About the Work](#What-the-Pilots-Taught-Me-About-the-Work)

## Introduction

I built an AI-assisted workflow for turning release notes into docs updates.

When a release affected existing docs, the workflow moved through a check for docs impact, a maintenance report, a scoped plan, and a final docs change.
What made the workflow useful was the infrastructure around it: the artifacts, handoffs, and review systems that made the output trustworthy enough to use.

A workflow can produce plausible text and still waste reviewer time if it hides context, buries evidence, or makes review slower than it needed to be.
Reviewers end up reviewing sources, reconstructing intent, and remaking decisions that the workflow should already have carried forward.

In this project, context engineering meant designing the context that the model would use so that reviewers could trust what it produced.

## Documentation Work Breaks When Context Stays Implicit

AI can make drafting faster, but a reviewer still needs clear scope, evidence, target pages, and claim boundaries.
If those stay implicit, then the time saved during generation just gets spent on review.

That was the core problem in this project. I was not trying to get an agent to publish polished docs on its own. I was trying to build a workflow that a reviewer would choose over a blank page.

## What I Mean by Context Engineering

Context engineering means going beyond just tweaking prompts. You need to design the workflow's artifacts and handoffs:
1. A release note had to become a docs-impact assessment, which was a short check of whether the change affected existing docs.
1. That assessment had to become a maintenance report, which was a prioritized list of updates.
1. The report then had to become a scoped plan for one update and then a docs change a reviewer could trace back to evidence.

Each handoff needed clear inputs, a defined output shape, and the right evidence.

## Why the Workflow Needed Structure

That sequence became a release-report-to-docs-maintenance chain.
Each stage had a specialized skill, an output template, and a way to review the result.
Later, I added a short synthesis artifact that summarized one full run.
The goal was to make repeated runs comparable so I could see where trust, scope, or usability still broke down.

The workflow was operational thanks to the consistency of the handoffs.
Without that, I would have had scattered examples of model output instead of a system I could evaluate.

## Reusable Output Was Still Too Slow to Trust

One early maintenance-report pilot narrowed a single docs-impacting change to one target page and a plausible `expand` recommendation.
That was already useful.
A reviewer could start from that instead of a blank page.

However, it was still slow to verify.
The report looked plausible, but it did not make it obvious what evidence to check first or why the change was marked with that level of urgency.

I fixed that by tightening the maintenance-report contract.
Evidence links had to be ordered from fastest trust check to supporting detail.
The `reason` field had to explain urgency when the label was not obvious.
The artifact needed to show a reviewer what to check first and why.

## A Correctly Blocked Artifact Is Still Useful

A later pilot produced a correctly blocked maintenance item.
The workflow could identify that docs work was needed, but it could not yet justify choosing a target page, so it kept the blocker visible instead of inventing a destination.
That exposed a flaw in the evaluation model, not in the maintenance item.
If usefulness only meant "ready to apply," then the workflow would reward unsafe confidence and punish correct caution.

I changed the rubric so blocked-but-correct output still counted as useful when it preserved the blocker clearly and prevented premature downstream work.

## Strong Stage Artifacts Can Still Create Workflow Friction

Once the workflow could run end to end, a different weakness appeared.

In one run, the assessment, maintenance report, plan, and apply checklist all agreed on the same change across three related configuration pages.
However, the run was slow to review.
A reviewer would have to jump between those artifacts and the edited pages to piece together the whole story.
The problem was not in any one file but in the gaps between them.

I fixed that problem by adding a short synthesis artifact that summarized the whole run in one place.
After the next pilot showed it reduced navigation cost, I kept it.

## What the Pilots Taught Me About the Work

The main lessons were about what a workflow needs before people will trust and reuse it:
- Clear signals about where to look first
- Evaluation logic that handles blocked states correctly
- A short chain-level summary when review spans too many files

You need a system that makes outputs interpretable, reviewable, and reusable across runs.

For docs teams, reliable AI-assisted docs-maintenance work depends less on one clever generation step and more on the structure around it: the artifacts, handoffs, and review systems that make output trustworthy enough to use.

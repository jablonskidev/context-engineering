# Context Artifacts Without Reinventing Technical Writing

**Table of Contents:**
- [Introduction](#Introduction)
- [Why duplication happens](#Why-duplication-happens)
- [How rot happens](#How-rot-happens)
- [What technical writing figured out](#What-technical-writing-figured-out)
- [How to prevent rot](#How-to-prevent-rot)
- [What to do](#What-to-do)
- [Beyond duplication](#Beyond-duplication)

## Introduction

Context engineering is rediscovering problems that technical writing has been solving for decades.

I've worked on both sides. I ran editorial direction for [Real Python](https://realpython.com/) for three and a half years, shipping two written tutorials and one video course every week to more than three million monthly visitors. I'm a founding member of the [Python Documentation Editorial Board](https://peps.python.org/pep-0732/). I'm now a Context Engineer at RelationalAI.

Although I had different titles, I kept seeing the same problems. Context artifacts often duplicate content that's maintained elsewhere. Technical writers learned that needed to be avoided decades ago.

**Definition:** Context artifacts are the files we maintain for models and agents to consume. They include system prompts, tool descriptions, skills files, retrieval corpora, style guides.

## Why duplication happens

If you open a skills file, there's a good chance that you'll find content from the docs site mixed into it. An agent needs to know what an API accepts, so the API schema gets pasted into the tool description. An agent needs to understand a product concept, so a paragraph about that concept gets copied into the system prompt. An agent needs examples, so examples get written inline.

Careful engineers do this deliberately, thinking they are being thorough. But they are setting up the skills file to rot.

## How rot happens

Let's say a skills file guides an agent that helps users work with a time-tracking service. The file explains what projects and time entries are, describes how to log time, and lists the business rules, including that archived projects can't accept new time entries. It is thorough.

Six months later, the service adds a new project status: `on_hold`. Projects with this status also can't accept new time entries, with their own conditions. The product documentation is updated. The skills file is not because no one remembers that it duplicates the docs.

When a user tries to log time against an `on_hold` project, the agent consults the skills file, sees only two statuses, and tells the user that the request should work. The API rejects it. The agent tries again. The API rejects it again. The skills file has no guidance for this failure, because the status causing it doesn't exist in the skills file's model of the product.

This rot happened because content duplication created a maintenance obligation that no system enforced. It wasn't carelessness. No check in CI caught the divergence. No review process surfaced the parallel source.

## What technical writing figured out

Technical writing solved this problem with the single-sourcing movement of the 1990s and the adoption of DITA in the 2000s. When information lives in multiple places, it drifts. The answer was to write the content in one place, reference it from everywhere else, and maintain only the source.

Context engineering is currently making the mistake that single-sourcing was invented to solve.

## How to prevent rot

A skills file that points to the docs stays accurate as the docs are maintained. It should describe only what the docs can't: how the agent should behave, when to take specific actions, how to handle ambiguous user requests, or how to recover when something goes wrong.

In the example with the time-tracking service, a referencing skills file would point to the docs for project statuses, time entry fields, and business rules. The file itself would describe things the docs don't cover: how the agent should clarify underspecified requests, how to confirm assumptions with the user, how to recover when the API returns an error the skills file doesn't recognize.

When the time-tracking service adds `on_hold`, the docs are updated. The skills file doesn't need to be, because it wasn't describing statuses in the first place. The agent reads the updated docs through the reference and adapts.

Referencing works when the docs are maintained, stably identifiable, and retrievable at the right moment. It's not a small ask, but it's the right one because the alternative is unsustainable.

## What to do

When building a context artifact, default to referencing maintained sources rather than duplicating them. When the information has no maintained home, giving it one is the work. The artifact is built on top of good docs, not in place of them.

Write it once. Reference it from everywhere. Maintain the source.

## Beyond duplication

Avoiding duplication is just one lesson that context engineering can learn from technical writing. There are others: audience analysis, editorial judgment about what to include, and task orientation. Each one is a problem technical writing has spent decades solving. Start with what it already figured out.

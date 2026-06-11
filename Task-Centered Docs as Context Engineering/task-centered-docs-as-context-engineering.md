# Task-Centered Docs as Context Engineering

**Table of Contents:**
- [Introduction](#Introduction)
- [The Project](#The-Project)
- [Why It Worked](#Why-It-Worked)
- [Context Engineering in Practice](#Context-Engineering-in-Practice)
- [Conclusion](#Conclusion)

## Introduction

Context engineering is the practice of designing information so humans and AI systems can find, interpret, and use the right thing at the right moment.
Task-centered documentation is one practical way to do that.

Too many documentation sets are still organized around internal systems rather than user goals.
That forces users to translate product structure into the action they actually need to take.
When documentation mirrors how a system is built rather than what a user needs to do, navigation gets harder and retrieval gets noisier.
AI systems run into a similar problem: they work best when tasks, boundaries, and outcomes are explicit.

I addressed this problem in a recent documentation project.

## The Project

The brief was to restructure the admin-facing docs around tasks. The goal was to make the docs easier to navigate, maintain, and use well.

Before the restructure, [Build](https://docs.relational.ai/build/) already routed users by goal and workflow, but [Manage](https://docs.relational.ai/manage/) still routed administrators through internal components and infrastructure.
That meant users often landed in broad, component-led pages where concepts, operational details, and tasks were bundled together.
The redesign replaced that model with one organized around workflows, related tasks, and verb-led headings that made actions easier to find.

The work was not just about simplifying language.
It was about changing the shape of the information.
Pages became smaller and more task-centered, titles became more action-oriented, and developer-facing and administrator-facing paths became more clearly separated.

Progressive disclosure shaped the redesign.
Overview pages helped readers choose the next step, while task pages focused on helping them complete a specific action with only the context required to succeed.
Instead of repeating the same guidance across sections, pages linked back to canonical sources.

I used agents to help complete the project, but I defined the information model, task boundaries, naming conventions, and navigation logic.

The result was a documentation structure that reduced navigation friction, clarified task boundaries, and made the knowledge in the docs easier for both people and AI systems to act on.

## Why It Worked

This approach works by starting with the action a user is trying to take and supplying the context needed at that point.

For human readers, that lowers cognitive load. A user can scan for a verb, recognize the task that matches their goal, and move forward without decoding product structure or sorting through instructions meant for someone else.
Smaller pages reduce context switching, and clearer audience boundaries reduce noise.

For AI systems, the same structure produces better retrieval targets.
Broad, component-based pages often mix concepts, procedures, and audiences together, making them harder to chunk cleanly and harder to target precisely.
An intent-first structure creates clearer boundaries and makes relevant guidance easier to retrieve and use.

Humans and AI do not use documentation in the same way, but they benefit from many of the same structural choices: explicit headings, narrowly scoped pages, audience-separated workflows, and concepts linked from tasks instead of interrupting them.
The same move that improves usability for a person often improves retrieval quality for an agent.

## Context Engineering in Practice

In this project, context engineering meant shaping knowledge so it became usable at the moment of need, whether the audience was human or AI.
The real work was deciding what belonged together, what needed to be split apart, what users needed first, what could be deferred, and what language would make tasks easier to find and use.

## Conclusion

Good documentation does not just explain a system. It helps someone do something with it.

When the structure is organized around intent, documentation becomes easier for people to navigate and easier for AI systems to use well.
That is what makes task-centered docs more than good documentation design. It makes them usable infrastructure.

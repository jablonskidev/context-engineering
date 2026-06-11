# Task-Centered Docs as Context Engineering

**Table of Contents:**
- [Introduction](#Introduction)
- [The Structural Problem](#The-Structural-Problem)
- [Designing the New Structure](#Designing-the-New-Structure)
- [Making the Structure Executable](#Making-the-Structure-Executable)
- [Why It Worked](#Why-It-Worked)

## Introduction

Effective context engineering requires task-centered documentation. When docs are structured around tasks rather than concepts, both humans and AI systems can more easily find the right information at the right time.

Too many documentation sets still mirror how a product is built rather than what a user is trying to do. That forces readers to translate system structure into action. It also produces weaker retrieval targets for AI systems, which work best when tasks, boundaries, and outcomes are explicit.

## The Structural Problem

I recently reorganized the [Manage](https://private.relational.ai/manage/) section of a docs site around user tasks rather than product components. The goal was to make the docs easier for both humans and AI systems to use.

Before the rearchitecture, [Build](https://docs.relational.ai/build/) already routed users by goal and workflow, but [Manage](https://docs.relational.ai/manage/) still routed administrators through components. Users often landed in broad, component-led pages where concepts, operational details, prerequisites, and procedures were bundled together. To complete a task, they first had to understand how the documentation mirrored the underlying system.

That was a real problem. It was not just a matter of page length or writing style. The organizing model asked users to navigate by internal structure rather than by intent.

## Designing the New Structure

I replaced that component-led model with a task-centered one: workflows at the top level, narrower task pages underneath, and verb-led headings that made actions easier to scan.

I redesigned the information model. I broke broad pages into narrower task pages, used overview pages to route readers through a workflow, and rewrote page titles and task headings around actions. I also separated developer-facing and administrator-facing paths more clearly so each audience saw less irrelevant context. Instead of repeating the same guidance in multiple places, I linked to canonical sources and treated duplication as a structural problem rather than a writing convenience.

Those changes clarified task boundaries, reduced ambiguity about where content belonged, and made the documentation easier to extend without turning pages into catch-alls.

## Making the Structure Executable

The redesign worked because I turned it into an explicit system rather than a set of preferences.

I created shared scaffolds that encoded the rules of the new structure. An architecture document defined the information architecture philosophy, hierarchy model, naming conventions, placement heuristics, and agent-oriented retrieval assumptions. Shared templates defined the expected shape of overview pages and task pages. A planning template gave each generated page a standard scaffold before drafting began. I also created page-specific planning and research documents to define audience, task boundaries, workflow position, source material, and document type before generation.

Existing documentation pages served as source material where useful, but they also acted as boundary-setting references: material to extract from, link to, avoid duplicating, or keep on the other side of an audience or surface-area split.

Agents helped execute the project, but only within that scaffold. The high-leverage work was still defining the information model, deciding what counted as a task, determining what belonged together or needed to be split apart, selecting the right document type, naming pages consistently, and giving agents enough structure to produce usable output. The agents accelerated execution. They did not supply the architecture.

## Why It Worked

The new structure worked because it made tasks, boundaries, and relationships explicit.

For human readers, that reduced cognitive load. A reader could scan for a verb, recognize the task that matched their goal, and move forward without decoding product structure first. Smaller pages reduced context switching, and clearer audience boundaries reduced noise.

For AI systems, the same structure produced better retrieval targets. Broad, component-based pages often mix concepts, procedures, and audiences together, which makes them harder to chunk cleanly and harder to target precisely. A task-centered structure creates clearer boundaries and makes relevant guidance easier to retrieve and act on.

The supporting artifacts mattered for the same reason. The architecture document made placement and naming rules explicit. Templates constrained page shape. Planning documents supplied page-level scope and source boundaries. In other words, the agents worked better for the same reason the docs worked better: the structure was explicit enough to navigate, reuse, and execute.

Task-centered docs are essential for making operational knowledge more usable for both humans and AI systems.

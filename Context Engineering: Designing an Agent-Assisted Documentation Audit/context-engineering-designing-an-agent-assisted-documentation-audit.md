# Context Engineering: Designing an Agent-Assisted Documentation Audit

**Table of Contents:**
- [The reason for the audit](#The-reason-for-the-audit)
- [The standards I defined](#The-standards-I-defined)
- [The audit contract](#The-audit-contract)
- [The result](#The-result)
- [The improvement I'll add next time](#The-improvement-I'll-add-next-time)

## The reason for the audit

I had to reorganize a long docs page into multiple short pages structured around tasks rather than product features.
I needed to understand what we could reuse, what needed light editing, and what required more substantial work.

I conducted an agent-assisted audit to scope that work before committing to a rewrite so we could meet our documentation standards without slowing delivery more than necessary.

## The standards I defined

I gave the agent reference material:
- The architecture guide defined how content should support reader tasks and fit into the documentation structure.
- The style guide provided editorial conventions for clear, consistent writing.
- The product overview provided context about the product’s purpose and the problems it was meant to solve.

I also selected existing documentation pages as benchmarks.
These showed what the standards looked like in practice: how a page established prerequisites, sequenced actions, and explained results.
They also illustrated reusable fragments and templates.
I provided the agent with a variety of examples because I did not want a single format to be imposed indiscriminately.

Together, the references gave the agent standards to evaluate against and concrete examples to learn from.

# The audit contract

The audit needed to:
- Evaluate each section against its reader task
- Identify the severity of a problem as well as the effort required to fix it

I used a planning interview to make those requirements explicit.
The agent asked one question at a time and updated the plan as we defined the project.

I specified what feedback had to contain:
- Each section’s purpose
- Gaps and resulting problems for readers
- Content that could be kept
- The work required to fix the section

I wanted a report that would help me scope the work, so I needed nuanced feedback.
For example, a serious problem might need only a small correction, but an instructional gap could require an example or substantial restructuring. 

## The result

Here's how the audit report helped me:
- The section-by-section inventory connected each part of the guide to a reader task.
- The prioritized findings made reader consequences visible.
- The before-and-after examples made the proposed changes tangible.
- The recommended remediation order showed which corrections and prerequisites should come first.
- The contrast between severity and effort was especially useful for scoping work.

## The improvement I'll add next time

The next time I plan an audit like this, I will define the criteria for an MVP so that the agent can identify a small, valuable first version.

While the report distinguished kinds of remediation, it did not propose an MVP oh what it would look like to get the contents of that docs page to follow a minimum acceptable standard.
Defining one would require more context about what readers must be able to accomplish and which quality requirements cannot be deferred, so the assessment could distinguish first-version work from later improvements.

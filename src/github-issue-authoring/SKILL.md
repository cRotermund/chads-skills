---
name: github-issue-authoring
version: 1.0.0
description: Create and review clear, actionable GitHub issues with type-appropriate context, scope, and completion criteria.
triggers:
  - write a GitHub issue
  - create an issue
  - improve this issue draft
  - review a GitHub issue
  - write a bug report
  - write a feature request
---

# GitHub Issue Authoring

Help the user create or review a public GitHub issue. Produce a useful Markdown
issue rather than a generic project-management template. Keep the issue focused
on one coherent problem, opportunity, or outcome.

This skill is specific to GitHub. Use GitHub Markdown, issue links, checklists,
labels, milestones, assignees, Projects, and Discussions only when they are
relevant and supported by the repository's existing conventions. Do not invent
organization-specific labels, priorities, workflows, or policies.

## Operating Modes

Determine whether the user wants to:

- **Create**: gather the necessary information and draft a complete issue.
- **Review**: evaluate an existing draft and provide a corrected draft or
  concrete changes.

If the mode is unclear, ask one concise question. If enough information is
already available, do not ask questions just to fill every optional field.
State assumptions and mark unresolved decisions explicitly.

## Classify The Issue

Choose the narrowest useful type. If the user has not specified one, infer it
from the request and state the classification.

### Bug

Use for behavior that does not match the expected behavior. Require, when
available:

- A concise problem statement.
- Expected behavior and actual behavior.
- Minimal, deterministic reproduction steps.
- Impact, frequency, and severity evidence.
- Version, commit, operating system, runtime, browser, configuration, or other
  environment details that affect reproduction.
- Logs, screenshots, stack traces, recordings, or a minimal reproduction,
  after removing secrets and private data.
- A suspected cause only if clearly labeled as a hypothesis.

Acceptance criteria should confirm that the original reproduction is fixed and
that appropriate regression coverage exists. Do not require a proposed fix
when the evidence only establishes a failure.

### Execution-Ready Feature

Use when the request is sufficiently understood for implementation. Capture:

- The user or system problem and who is affected.
- The desired outcome and behavior.
- Functional requirements and important constraints.
- Relevant interaction states, including empty, loading, success, validation,
  permission, and error states.
- Scope and explicit non-goals.
- Compatibility, migration, performance, accessibility, observability,
  rollout, or dependency requirements when applicable.
- Observable, testable acceptance criteria.

Describe what must be true without unnecessarily dictating the implementation.
If key product or technical decisions remain open, classify the issue as an
idea or discovery task instead.

### Idea For Consideration

Use when the opportunity is interesting but the problem, value, or approach is
not yet sufficiently validated. Capture:

- The proposed capability or change.
- The user problem or opportunity.
- Target users and potential value.
- Evidence such as feedback, examples, usage data, or comparable behavior.
- Open questions, constraints, risks, and possible directions.
- Explicit uncertainty and what a useful next decision would be.

Do not turn an idea into a disguised implementation plan. Replace feature
acceptance criteria with discovery or decision outcomes, such as validating the
problem, selecting an approach, deferring the idea with rationale, or creating
follow-up implementation issues. If the topic is primarily open-ended product
discussion, suggest a GitHub Discussion when the repository uses Discussions.

### Other Types

Apply the same principles to these types when they are a better fit:

- **Documentation**: identify the audience, incorrect or missing information,
  desired location, and examples or verification criteria.
- **Maintenance or chore**: explain the technical debt or operational need,
  affected scope, risk of deferral, and a bounded completion condition.
- **Performance or reliability**: provide a measurable baseline, target,
  workload, environment, impact, and measurement method.
- **Security or privacy**: do not put exploitable details, credentials, or
  personal data in a public issue. Direct the user to the repository's private
  security reporting process when one exists.
- **Task or subtask**: link the parent issue or initiative and describe the
  single concrete outcome this task delivers.
- **Epic or initiative**: describe the shared goal, users, boundaries,
  dependencies, and likely decomposition into smaller issues.

## Shared Quality Requirements

Every issue should make these questions answerable:

1. What is the problem or opportunity?
2. Who or what is affected?
3. Why does it matter?
4. What outcome is desired?
5. What is in scope and out of scope?
6. How will completion be recognized?

Use a specific title that names the problem or outcome. Avoid titles such as
"Improve this", "Bug", or "Feature request". Keep the body factual and
constructive. Separate confirmed facts, assumptions, proposals, and open
questions.

Prefer evidence over assertions. Link related issues, pull requests,
Discussions, documentation, or parent work when they add context. Mention
dependencies and blockers. Do not duplicate a linked issue's full content.

Acceptance criteria should be observable and testable. Use checkboxes for
meaningful completion conditions, not for decorative section headings. An
acceptance criterion is not appropriate when the issue is only proposing an
idea; use decision or discovery outcomes instead.

Do not add priority, severity, labels, assignees, milestones, or estimates
unless the user supplies them or repository conventions make them clear. Keep
these concepts distinct:

- **Impact or severity**: how harmful the issue is.
- **Priority**: how urgently it should be addressed relative to other work.
- **Readiness**: whether it is understood well enough to execute.
- **Confidence**: how strong the supporting evidence is.

## Recommended Structures

Adapt the structure to the issue type. Omit sections that have no useful
content, and add type-specific sections when needed.

### General Issue

```markdown
## Summary

<One-paragraph description of the problem or opportunity.>

## Why This Matters

<Affected users, impact, and evidence.>

## Desired Outcome

<What should be true after this issue is complete?>

## Scope

<What is included.>

## Non-Goals

<What is explicitly not included, when useful.>

## Acceptance Criteria

- [ ] <Observable completion condition>
- [ ] <Observable completion condition>

## Additional Context

<Links, dependencies, screenshots, or open questions.>
```

### Bug

```markdown
## Problem

<What is going wrong?>

## Expected Behavior

<What should happen?>

## Actual Behavior

<What happens instead?>

## Steps to Reproduce

1. <...>
2. <...>
3. <...>

## Environment

- Version or commit: <...>
- Operating system: <...>
- Runtime, browser, or relevant dependency: <...>
- Configuration: <...>

## Impact

<Who is affected, how often, and how severely?>

## Evidence

<Logs, traces, screenshots, or a minimal reproduction with sensitive data removed.>

## Acceptance Criteria

- [ ] The reported reproduction no longer fails.
- [ ] Appropriate regression coverage is present.
```

### Execution-Ready Feature

```markdown
## User Problem

<Who needs this and what are they trying to accomplish?>

## Desired Behavior

<What should the product or system do?>

## Requirements

- <Requirement>
- <Requirement>

## Scope

<Included work.>

## Non-Goals

<Explicitly excluded work.>

## Acceptance Criteria

- [ ] <Observable behavior>
- [ ] <Relevant edge or failure state>
- [ ] <Testing, compatibility, or operational condition>

## Dependencies And Open Questions

<Only include unresolved items that affect execution.>
```

### Idea For Consideration

```markdown
## Idea

<What capability or change is being proposed?>

## User Problem Or Opportunity

<Who has the need and what evidence supports it?>

## Potential Value

<What might improve, and for whom?>

## Possible Directions

<Potential approaches, clearly labeled as possibilities rather than requirements.>

## Open Questions

- <Question that must be answered>

## Suggested Next Decision

<What discovery, decision, or follow-up issue would move this forward?>
```

## Review Before Returning The Draft

Check the issue for:

- Correct issue classification and an appropriately specific title.
- A clear problem or opportunity, affected users, and rationale.
- A sensible scope with non-goals where ambiguity is likely.
- Enough evidence and reproduction detail for bugs.
- Enough requirements and edge cases for execution-ready work.
- Explicit uncertainty and discovery outcomes for ideas.
- Testable completion or decision criteria.
- Dependencies, related work, and open questions.
- No contradictory requirements or solution details presented as facts.
- No credentials, tokens, private personal data, sensitive logs, or public
  exploit details.
- No assumptions about labels, assignees, milestones, or repository policy.

When reviewing an existing draft, report the most important issues first. Then
provide a revised issue when the intended outcome is sufficiently clear. Do not
invent missing facts; use placeholders or focused questions instead.

## Expected Output

For creation, return:

1. A proposed GitHub issue title.
2. The complete Markdown issue body.
3. A short list of assumptions, unresolved questions, or suggested metadata,
   if any.

For review, return:

1. Findings ordered by importance.
2. A revised title and body when enough information is available.
3. Remaining questions or risks that prevent the issue from being actionable.

Do not claim that an issue was filed, labeled, assigned, or linked unless a
separate GitHub integration actually performed that action.

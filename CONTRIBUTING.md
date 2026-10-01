# Contributing

Thank you for helping build Chad's Skills. This guide covers skill authoring,
review, and repository changes. For the collection's purpose and runtime
usage, start with the [`README.md`](README.md).

## Before You Start

- Read the relevant sections of [`README.md`](README.md).
- Check existing skills before creating a new one or changing its name.
- Search existing issues and pull requests before opening duplicate
  discussions.
- If a change depends on server behavior, read the corresponding
  `skills-mcp-go` documentation before authoring the skill.

## Repository Structure

All distributable skill definitions belong under `src/`. A skill normally has
this shape:

```text
src/<skill-name>/
├── SKILL.md
├── scripts/    # optional readable text assets
└── tools/      # optional readable text assets
```

Keep repository-level documentation at the root. Do not add implementation
code or server configuration to this repository unless the project scope is
explicitly expanded.

## Creating or Updating a Skill

1. Create a focused branch from the current default branch.
2. Add or update one skill directory under `src/`.
3. Keep `SKILL.md` frontmatter valid and complete.
4. Explain the skill's workflow, boundaries, and expected output in the body.
5. Add or update readable text assets only when they are part of the skill's
   documented contract.
6. Update documentation when the repository workflow or skill format changes.
7. Run the validation described in the README and inspect the final diff.

Keep each branch limited to one logical change. A skill rename should be
treated as a compatibility-sensitive change because clients may refer to its
name directly.

## Skill Quality Checklist

- The skill has a unique, descriptive `name`.
- The `version` is a valid semantic version and reflects the change.
- The `description` states the capability clearly.
- Triggers describe realistic requests that should activate the skill.
- Instructions are actionable, ordered where order matters, and free of
  contradictory requirements.
- Tool references are limited to tools the consuming environment can provide.
- Examples and paths are portable and do not expose private data.
- Scripts and tools are treated as text assets; they are not assumed to run by
  `skills-mcp-go`.

## Branches

Do not commit or push directly to `master`. Start from an up-to-date `master`
branch and use a focused branch with one of these prefixes:

```text
feature/<short-description>
fix/<short-description>
docs/<short-description>
chore/<short-description>
```

## Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(optional scope): short imperative description
```

Common types include `feat`, `fix`, `docs`, `test`, `refactor`, and `chore`.
Keep commits cohesive and do not combine unrelated documentation or skill
changes.

Examples:

```text
feat(skill): add release-notes workflow
fix(database-skill): clarify migration safety checks
docs: explain local skill validation
```

## Pull Requests

Open a pull request instead of merging directly to `master`. Include:

- A concise summary of what changed and why.
- The skill names and versions affected.
- Validation performed, including any runtime checks against
  `skills-mcp-go`.
- Known limitations, compatibility concerns, or follow-up work.

Before requesting review, confirm that the branch contains only the intended
changes and that the pull request description matches the implementation.

## Documentation Ownership

Keep human-facing setup, format, and usage information in `README.md`. Keep
contributor workflow in this file. Keep agent navigation and operating rules
in `AGENTS.md`; it should point to these documents rather than copy them.

## Community Expectations

Be specific, constructive, and respectful in issues, reviews, and discussions.
Explain the problem before prescribing a solution, and provide enough context
for another contributor to evaluate a proposed skill safely.

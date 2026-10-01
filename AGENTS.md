# Agent Guidance

This file is an agent-facing index and operating policy. It intentionally does
not duplicate human setup, skill authoring, or contribution prose.

## Read First

- Project purpose, layout, skill format, runtime usage, and validation:
  [`README.md`](README.md).
- Branches, authoring workflow, review checklist, commits, and pull requests:
  [`CONTRIBUTING.md`](CONTRIBUTING.md).
- Server behavior, MCP interfaces, and security boundaries:
  the `skills-mcp-go` repository documentation.

Read the relevant source document before making changes. Treat those documents
as the source of truth rather than copying their contents into this file.

## Repository Map

- `src/` contains the checked-in skill definitions.
- `src/<skill-name>/SKILL.md` is the required entrypoint for each skill.
- `src/<skill-name>/scripts/` and `src/<skill-name>/tools/` may contain
  optional readable text assets.
- `README.md` and `CONTRIBUTING.md` own human-facing documentation.

## Agent Operating Rules

- Preserve user changes in the worktree; never revert unrelated work.
- Use focused edits and keep each change limited to one logical purpose.
- Read the relevant skill before modifying it and preserve its established
  contract unless the change explicitly updates that contract.
- Do not add secrets, private data, generated binaries, or machine-specific
  paths.
- Do not assume that skill scripts or tools will be executed by the MCP server.
- Do not broaden the `skills-mcp-go` server's authority or MCP surface from
  this repository.
- Do not stage, commit, push, or open a pull request unless explicitly
  requested.
- Before reporting completion, run the applicable validation from
  `README.md` and disclose environment limitations.

## Documentation Ownership

- Update `README.md` for human users and the skill format.
- Update `CONTRIBUTING.md` for contributor workflow and review expectations.
- Update this file only for agent-specific navigation or operating rules.

# Chad's Skills

Chad's Skills is a collection of Markdown-based skills for use with the
`skills-mcp-go` server and compatible MCP clients. Skills
are stored under `src/` and are discovered from that directory at runtime.

## Repository Status

This repository is being bootstrapped. The `src/` directory is ready for the
first skill definitions.

## Repository Layout

```text
.
├── src/                  # Skill definitions served by skills-mcp-go
│   └── <skill-name>/
│       └── SKILL.md
├── AGENTS.md             # Agent-facing navigation and operating policy
├── CONTRIBUTING.md       # Contribution workflow and authoring guidance
└── README.md             # Human-facing project documentation
```

Each skill should live in its own directory. Optional readable text assets may
be placed in that skill's `scripts/` or `tools/` directories when they are
useful to an MCP client.

## Quick Start

### Add a skill

Create a directory below `src/` and add a `SKILL.md` file:

```text
src/
└── example-skill/
    └── SKILL.md
```

Use YAML frontmatter with the required fields `name`, `version`, and
`description`:

```markdown
---
name: example-skill
version: 1.0.0
description: A concise description of the capability this skill provides.
triggers:
  - example task
allowed_tools:
  - read_file
---

# Example Skill

Describe the workflow, constraints, and expected output here.
```

The `triggers` and `allowed_tools` fields are optional. Use semantic versions
for `version`, and make `name` match the skill's stable identity rather than
its current implementation detail.

### Run with skills-mcp-go

Build or obtain the `skills-mcp-go` server, then point `SKILLS_DIR` at this
repository's `src/` directory:

```text
SKILLS_DIR=/absolute/path/to/chads-skills/src skills-server
```

On Windows PowerShell:

```powershell
$env:SKILLS_DIR = "C:\path\to\chads-skills\src"
.\skills-server.exe
```

The server discovers valid skills in `src/` and exposes them through its MCP
resources, prompts, and tools. Refer to the `skills-mcp-go` README for server
build instructions, MCP client configuration, supported asset behavior, and
runtime diagnostics.

## Authoring Guidelines

- Give each skill one focused purpose.
- Write instructions that are explicit about inputs, steps, constraints, and
  expected results.
- Keep frontmatter concise and accurate.
- Use stable semantic versioning and increment the version when the skill's
  behavior or contract changes.
- Treat skill content as instructions for a consuming agent, not as executable
  code for the MCP server.
- Add only readable text assets under `scripts/` and `tools/`; the server does
  not execute them.
- Avoid secrets, credentials, machine-specific paths, and unrelated generated
  files.

## Validation

Before opening a pull request, verify that each changed skill:

- Has a `SKILL.md` file in a directory below `src/`.
- Contains valid YAML frontmatter with `name`, `version`, and `description`.
- Uses a unique `name` and version combination.
- Contains instructions that match its description and trigger phrases.
- Does not include secrets or unsafe assumptions about the target workspace.

For runtime validation and the complete server test suite, use the commands
and guidance in the `skills-mcp-go` repository's `CONTRIBUTING.md`.

## Contributing

Bug reports, improvements, and new skills are welcome. Read
[`CONTRIBUTING.md`](CONTRIBUTING.md) before preparing a change. Agent-based
contributors should start with [`AGENTS.md`](AGENTS.md).

## License

No license has been selected for this repository yet. Until one is added,
existing content should not be assumed to be available for unrestricted reuse.

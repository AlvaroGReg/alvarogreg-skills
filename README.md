# alvarogreg-skills

A collection of reusable skills and slash commands for coding agents. Skills follow the [Agent Skills format](https://agentskills.io/) and can be installed with [`skills`](https://skills.sh).

## Repository contents

```text
commands/                 Reusable slash-command prompt templates
skills/<skill-name>/      Reusable skills; each contains a SKILL.md
templates/SKILL.md        Starter template for a skill
.opencode/commands/       Commands for managing this repository's content
```

The files in `commands/` are reusable prompts, not skills. The repository-local `/config-skill` and `/config-skills` commands in `.opencode/commands/` help install selected repository commands and skills into another project or globally; they are not part of the reusable command collection.

## Skills

Skills provide reusable instructions that an agent can load when relevant. Each skill lives in its own directory and has a `SKILL.md` entry point.

### Install skills

Install all skills in the current project:

```bash
npx skills add AlvaroGReg/alvarogreg-skills
```

Install one skill:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --skill <skill-name>
```

Install globally so the skills are available across projects. Use `--agent` to target an agent:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --global --agent opencode
npx skills add AlvaroGReg/alvarogreg-skills --global --agent claude-code
```

List the available skills before installing:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --list
```

To install a selected skill globally for one agent, combine `--skill`, `--global`, and `--agent`:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --skill <skill-name> --global --agent opencode
```

See the [`skills` documentation](https://skills.sh/docs) for supported agents and options.

### Available skills

| Skill | Purpose |
| --- | --- |
| `dockerino-lamborgino-cli` | Operate the Dockerino Lamborgino Windows application through its CLI. |
| `pre-release-development` | Make pre-release development and operational decisions involving compatibility, migrations, test databases, and rollout. |

### Create a skill

Create a directory under `skills/` with a `SKILL.md` file:

```text
skills/
└── skill-name/
    └── SKILL.md
```

Use [`templates/SKILL.md`](templates/SKILL.md) as a starting point, or scaffold a skill with:

```bash
npx skills init skills/skill-name
```

Use a unique, lowercase, hyphen-separated `name` in the frontmatter and a specific `description` that explains what the skill does and when it should be used. Keep the instructions focused on the skill's task.

## Commands

Commands are prompt templates invoked by slash command, such as `/sdd-status`. The reusable source files are in `commands/`. OpenCode discovers project commands from `.opencode/commands/` and global commands from `~/.config/opencode/commands/`; Claude Code uses `.claude/commands/` for a project and `~/.claude/commands/` globally.

Copy the commands into the target agent's command directory. For example, to make them available to OpenCode in the current project:

```bash
mkdir -p .opencode/commands
cp commands/*.md .opencode/commands/
```

For global OpenCode commands:

```bash
mkdir -p ~/.config/opencode/commands
cp commands/*.md ~/.config/opencode/commands/
```

For Claude Code, use `.claude/commands/` or `~/.claude/commands/` instead. Command metadata and prompt features can differ between agents; review and adapt a command's frontmatter and instructions for the target agent before copying it.

### Available commands

| Command | Purpose |
| --- | --- |
| `/goal <goal>` | Carry a goal through investigation, implementation, and verification. |
| `/update-subagents` | Review model assignments and propose updates; waits for approval before editing. |
| `/sdd-spec <spec-folder> <idea>` | Interview the user and draft a spec without writing code. |
| `/sdd-clarify <spec-folder>` | Review a spec for ambiguities, contradictions, uncovered edge cases, and constitution conflicts. |
| `/sdd-change <spec-folder> <requirement>` | Update a spec with a requirement and wait for approval before proceeding. |
| `/sdd-constitution [context]` | Propose project principles and wait for approval before writing the constitution. |
| `/sdd-plan <spec-folder>` | Create a technical plan for an approved spec. |
| `/sdd-tasks <spec-folder>` | Break a plan into small, verifiable tasks. |
| `/sdd-implement <spec-folder> <task>` | Implement one task, tests first, then stop. |
| `/sdd-verify <spec-folder>` | Verify implementation against the spec, plan, tasks, and constitution without editing files. |
| `/sdd-status <spec-folder>` | Report the spec's current SDD phase, task progress, and next command. |

For example, `/sdd-spec 002-export Add CSV export` starts a spec named `002-export` with “Add CSV export” as the initial idea. OpenCode commands can use positional placeholders such as `$1` and `$2`; `$ARGUMENTS` represents the full argument string.

### Create a command

Add a Markdown file to `commands/`, for example `commands/check.md`:

```markdown
---
description: Check a change against project requirements
agent: plan
---
Review $ARGUMENTS against the project requirements. Report findings only.
```

The filename becomes the slash-command name (`check.md` becomes `/check`). Use a concise `description`; add agent-specific metadata only when supported by the target. OpenCode supports `$ARGUMENTS` for all arguments and positional placeholders such as `$1` and `$2`.

## License

Individual skills may have their own licenses or usage terms. Check each skill before reusing it.

---
description: Configure commands and skills from this repository for a coding agent
agent: build
---
Configure the requested command(s) and/or skill(s) from this repository for the requested coding agent and scope.

Request: $ARGUMENTS

Use this repository as the source of truth:
- Reusable commands are in `commands/*.md`.
- Reusable skills are in `skills/*/SKILL.md`.

Workflow:
1. Identify the requested coding agent (for example, OpenCode or Claude Code), the requested item(s), and the scope. “Globally” means the user's global configuration; “for this project” means the specified project. Treat “available on my desktop tool” as global unless the user specifies a project. If the agent, item, or scope is otherwise ambiguous, ask a concise clarification before changing files.
2. For “all commands” or “all skills,” enumerate the matching source files and show which items will be configured. Do not confuse commands with skills.
3. Inspect the target's existing instructions and configuration. Use that agent's native, currently supported locations and formats; consult its official documentation if uncertain. For OpenCode, project commands belong in `.opencode/commands/` and global commands in `~/.config/opencode/commands/`. For skills, prefer the `npx skills` CLI and target the requested agent and scope; otherwise use a supported native skills directory.
4. Configure only the requested items. Preserve their behavior and argument placeholders, adapting metadata only as needed for the target agent. Do not edit this repository's source commands or skills. Do not overwrite a different existing file: report the conflict and ask before replacing it.
5. Verify the resulting files and report the target agent, scope, configured items, paths, and any conflicts or unavailable items.

Make the requested changes without asking for redundant confirmation. Ask before overwriting existing content or when the target cannot be determined safely.

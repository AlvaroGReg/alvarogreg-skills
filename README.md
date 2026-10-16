# alvarogreg-skills

A personal collection of reusable skills for coding agents, compatible with the Agent Skills format and [`skills.sh`](https://skills.sh).

## Installation

Install all skills from this repository in the current project:

```bash
npx skills add AlvaroGReg/alvarogreg-skills
```

Install a specific skill:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --skill <skill-name>
```

To make skills available globally across projects, add `--global` (or `-g`). You can target a specific agent with `--agent`, for example `--agent opencode` or `--agent claude-code`.

List available skills before installing them:

```bash
npx skills add AlvaroGReg/alvarogreg-skills --list
```

## Adding a skill

Each skill should live in its own directory under `skills/` and contain a `SKILL.md` file:

```text
skills/
└── skill-name/
    └── SKILL.md
```

Use [`templates/SKILL.md`](templates/SKILL.md) as a starting point. The frontmatter `name` must be unique, lowercase, and use hyphens between words. Write a specific description explaining what the skill does and when it should be activated.

You can also generate the structure with the CLI:

```bash
npx skills init skills/skill-name
```

See the [skills.sh documentation](https://skills.sh/docs) for more information about the format and supported agents.

## License

Individual skills may have their own licenses or usage terms. Check each skill before reusing it.

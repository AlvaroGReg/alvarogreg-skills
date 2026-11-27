---
description: SDD · Interview and generate the spec (use — /sdd-spec 002-idea-name)
agent: plan
---
Do NOT write code at any point. Read docs/constitution.md and MEMORY.md, and use the sdd skill.

Spec folder: specs/$1/
Initial idea (the text after the folder name): $ARGUMENTS

Your task:
1. Ask me questions ONE AT A TIME to remove ambiguities (edge cases, errors, what is out of scope for this version). Maximum 5 questions.
2. With my answers, generate specs/$1/spec.md following the sdd skill's template, with requirements in EARS and "Status: draft".
3. Only cover WHAT and WHY: no stack, architecture, or file names.

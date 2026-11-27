---
description: SDD · Generate the technical plan for an approved spec
agent: plan
---
Read docs/constitution.md, AGENTS.md, and specs/$1/spec.md. Use the sdd skill.
Do NOT write code.

Generate specs/$1/plan.md with: files to create or modify and each file's responsibility; pure logic functions (using "today" as a parameter); the algorithm in pseudocode; how the interface will be rendered; justified technical decisions (including the rejected alternative); and a test strategy using node --test.

Everything must respect the constitution and cover all FRs. Mark which FR each part covers. If the spec is not approved or has open questions, stop and tell me.

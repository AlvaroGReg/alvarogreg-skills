---
description: SDD · Verify the implementation against the spec
agent: plan
---
Verify the implementation for specs/$1/ against specs/$1/spec.md, specs/$1/plan.md, specs/$1/tasks.md, docs/constitution.md, and the sdd skill.

1. Check every FR against the implementation and identify any missing, incomplete, or out-of-scope behavior.
2. Check that the implementation follows the plan and constitution.
3. Run node --test and report the result.
4. Report task completion status and any discrepancies, citing the relevant FRs and files.

Do NOT modify any files or fix issues. Report findings only.

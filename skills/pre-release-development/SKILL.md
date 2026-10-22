---
name: pre-release-development
description: Use for pre-release changes that raise backward-compatibility, schema migration, or version-transition decisions, or when such a change requires establishing the release stage. Avoids unnecessary compatibility and production-transition code while preserving sound engineering practices.
---

# Pre-release development

## When to use

Use this skill only when a planned change raises a meaningful question about backward compatibility, old data or schemas, API or configuration transitions, or production rollout machinery. Do not load it just because the application is pre-release; routine changes with no such implications do not need this skill.

## Establish release stage

1. Check project documentation and agent instructions for an explicit release stage (for example, `pre-release` or `in production`). Do not infer it from the absence of release notes or compatibility requirements.
2. If the stage is missing or ambiguous, ask the user whether the application has been released. Once confirmed, record the answer in the most relevant existing project documentation; create a concise entry in an appropriate project document if none exists. Then continue.
3. Apply this skill's pre-release guidance only when the application is confirmed pre-release. If it has been released, follow its documented compatibility and operational policies.

## Make the change

- Implement the clean, coherent design for the current product. Do not retain obsolete contracts or add compatibility shims, aliases, duplicate code paths, feature flags, or transitional rollout machinery solely to support unreleased versions.
- Change APIs, configuration, and data models directly when needed. Use the project's normal migration mechanism for reproducible schema changes, but do not add migrations or rollout steps solely to preserve compatibility with older unreleased schemas. Never assume persistent data can be discarded; ask before destructive changes.
- Prefer a durable implementation over a quick workaround. Update affected code, documentation, and tests to match the current design; run the relevant checks.

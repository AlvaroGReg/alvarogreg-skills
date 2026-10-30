---
name: pre-release-development
description: Use for pre-release changes and operational setup involving backward compatibility, schema migrations, test databases, configuration transitions, or production rollout decisions. Helps avoid unnecessary infrastructure and transition work while preserving data safety.
---

# Pre-release development

## When to use

Use this skill when a planned change or developer workflow raises a meaningful question about backward compatibility, old data or schemas, API/configuration transitions, test database setup, or production rollout machinery. This includes configuring integration-test connection strings, deciding whether tests can use an existing development/test database or need a separate one, and running migrations against a local database. Do not load it just because the application is pre-release; routine changes with no such implications do not need this skill.

## Establish release stage

1. Check project documentation and agent instructions for an explicit release stage (for example, `pre-release` or `in production`). Do not infer it from the absence of release notes or compatibility requirements.
2. If the stage is missing or ambiguous, ask the user whether the application has been released. Once confirmed, record the answer in the most relevant existing project documentation; create a concise entry in an appropriate project document if none exists. Then continue.
3. Apply this skill's pre-release guidance only when the application is confirmed pre-release. If it has been released, follow its documented compatibility and operational policies.

## Make the change

- Implement the clean, coherent design for the current product. Do not retain obsolete contracts or add compatibility shims, aliases, duplicate code paths, feature flags, or transitional rollout machinery solely to support unreleased versions.
- Change APIs, configuration, and data models directly when needed. Use the project's normal migration mechanism for reproducible schema changes, but do not add migrations or rollout steps solely to preserve compatibility with older unreleased schemas. Never assume persistent data can be discarded; ask before destructive changes.
- For test-database setup, inspect the test code and project instructions to determine what the tests actually do and what kind of database they require. Treat terms such as “disposable” as a data-safety requirement, not an automatic requirement to provision an additional database: ask whether the user's existing database is dedicated to development/testing and whether its data may be changed before recommending extra infrastructure. If the user confirms it is an appropriate test database, configure tests to use it; do not keep insisting on a parallel database unless the tests or tooling require one or the user requests isolation. Clearly state when migrations or test writes will affect the selected database.
- Prefer a durable implementation over a quick workaround. Update affected code, documentation, and tests to match the current design; run the relevant checks.

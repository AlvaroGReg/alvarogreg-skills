---
name: dockerino-lamborgino-cli
description: Use the Dockerino Lamborgino Windows application through its CLI to register and configure projects, manage explicitly associated Git repositories, run saved launch actions, and inspect or stop managed terminal sessions. Use when the user asks to operate this application by command line or asks an agent to configure it.
---

# Dockerino Lamborgino CLI

Use this skill when operating the Dockerino Lamborgino application through its
CLI. The application targets Windows. A framework-dependent CLI requires the
.NET 10 Windows Desktop Runtime; self-contained published builds bundle .NET.
For development commands, install the .NET 10 SDK and run commands from the
repository root.

## Operating principles

- Follow the user's request and any applicable project instructions. Inspect
  project files only when the user directs you to do so or supplies criteria
  that call for it. The application itself does not discover projects or infer
  configuration.
- Register only the explicitly selected project directory. Do not scan child
  directories or invent paths, commands, action names, or repository roots.
- Registering and saving configuration do not run configured commands. Launch
  only the exact saved action the user requests.
- Treat configured commands as executable code with their configured effects.
  Before launching, inspect the selected action and explain relevant known
  effects when needed.
- Use project, action, launch, and session IDs returned by the application.
  Never use arbitrary PIDs or terminate processes outside a managed session.
- Git changes are explicit operations. Do not fetch, pull, or switch branches
  unless requested. Never run push, commit, stash, reset, or automatic conflict
  resolution through this workflow.
- Do not claim commands succeeded based only on starting them. Inspect their
  JSON result, exit code, operation status, or session output as appropriate.

## Find and run the CLI

From the application source directory, use:

```powershell
$cli = @("run", "--project", ".\src\DockerinoLamborgino.Cli", "--")
dotnet @cli list
```

The examples below use this `$cli` array. Use `--help` to display syntax and
`--version` to display the CLI assembly version. A built
`DockerinoLamborgino.Cli.exe` can be used instead. For commands that communicate
with the application, the application executable must be beside the CLI, or
`DOCKERINO_APP_PATH` must point to its absolute path. The CLI and application
use a current-user named pipe. For isolated instances, set the same
`DOCKERINO_PIPE_NAME` in both processes.

The project store defaults to
`%LOCALAPPDATA%\DockerinoLamborgino\projects`. An absent store is empty.
The application also accepts `--project-storage <absolute-directory>` for
isolated storage.

## Register and configure a project

Register a project with its name and an existing absolute directory:

```powershell
$result = dotnet @cli register "Example" "C:\My Projects\Example" | ConvertFrom-Json
$projectId = $result.project.id
$revision = $result.revision
```

Registration creates an empty action list; it does not inspect the directory or
execute commands. Save the returned project object to a separate JSON file,
edit it explicitly, validate it, and save using the revision returned by
`register` or a fresh `get`:

```powershell
$projectFile = ".\project-edit.json"
$result.project | ConvertTo-Json -Depth 20 | Set-Content -Encoding utf8 $projectFile
# Edit project-edit.json with the user-provided configuration.
dotnet @cli validate $projectFile
dotnet @cli save $projectFile $revision
```

Useful read operations:

```powershell
dotnet @cli list
dotnet @cli get $projectId
```

`get` and `list` return JSON including validation issues. Check those issues
before relying on a project's configuration. `list` can return successfully
while reporting problems for individual entries.

### Configuration shape

The JSON file contains one project object with:

- `id`: nonempty unique GUID.
- `name`: nonblank name.
- `directory`: existing absolute project directory.
- `repositories`: optional array of distinct existing absolute repository-root
  paths. Add only repositories the user explicitly associates; they may be
  outside the project directory.
- `actions`: array, which may be empty. Each action has a unique GUID `id`,
  nonblank `name`, `color` in `#RRGGBB` format, optional non-null `description`,
  optional `isQuickLaunch` boolean (defaults to `false`), and a nonempty
  `processes` array. `isQuickLaunch: true` shows a play button for that action
  beside the project name in the dashboard.
- Each process has a unique GUID `id`, nonblank `name`, an existing absolute
  `workingDirectory`, and a nonempty `steps` array.
- Each step has a unique GUID `id`, a nonblank `command`, and optional
  `dependsOnSuccessOf` referring to an earlier step in that process.

Generate unique IDs explicitly, for example with `[guid]::NewGuid()`. Steps run
in array order. If one fails, later steps in that process are skipped; other
processes continue independently. A long-running step blocks later steps in
its process. Only saved commands in the selected action run.

Unknown JSON properties are rejected; property names are case-insensitive.
Descriptions may be omitted but must not be `null`. Do not fill missing values
with guesses.

`save <json-file> <expected-revision>` rejects stale revisions. Reload with
`get`, reconcile the latest project data, and validate again before retrying.
`save <json-file>` without a revision is create-only; do not use it to overwrite
an existing project. Exit codes are `0` for success, `1` for an operation or
validation error, and `2` for invalid CLI syntax.

After registering or saving through the CLI, refresh the application project
list. This does not restore or activate the dashboard; if the app is not
running, the CLI starts it with only the floating launcher visible:

```powershell
dotnet @cli projects refresh $projectId
dotnet @cli dashboard open
```

## Git repository operations

The Dockerino CLI only exposes batch fetch and fast-forward-only pull because
these operations use the project's associated repositories and application
guardrails. For status or branch switching, use the application UI or invoke
Git directly in the repository. For Dockerino fetch/pull, use only repository
paths listed on the explicitly selected project. Obtain those paths from `get`;
do not discover or infer additional repositories.

```powershell
dotnet @cli git fetch $projectId "C:\My Projects\Example" "C:\Other\Repository"
dotnet @cli git pull $projectId "C:\My Projects\Example" "C:\Other\Repository"
```

Fetch and pull are explicit batch operations. Each repository must be
associated with the project. A failure for one repository does not prevent
processing the others. Fetch uses the current branch's upstream remote,
or the sole configured remote if there is no upstream; it reports missing or
ambiguous remotes instead of guessing. Pull requires an upstream and is
fast-forward-only. It may update remote-tracking references before reporting
that a divergent pull cannot proceed. Git prompts are disabled in this
integration: authenticate separately or configure noninteractive credentials.
Git hooks and credential helpers are not sandboxed.

## Launch and manage sessions

Read the project and its saved actions first. Start only the action the user
requested, using its IDs:

```powershell
dotnet @cli launch start $projectId $actionId
dotnet @cli launch list
dotnet @cli launch inspect $launchId
dotnet @cli session list
dotnet @cli session inspect $sessionId
dotnet @cli session output $sessionId
dotnet @cli session follow $sessionId
```

Launch starts asynchronously and returns managed launch/session IDs. Inspect
the launch or sessions to determine progress and step results. Use `output` for
retained output or `follow` for live output. Output can contain raw terminal
control sequences.

Stopping is scoped to an application-managed ID:

```powershell
dotnet @cli session stop $sessionId
dotnet @cli launch stop $launchId
```

Warn the user when stopping a launch or session will interrupt active work.
Sessions and their output are held in memory for the application lifetime;
they do not persist across restarts. Exiting the application stops its managed
sessions. Stopping a terminal session does not stop detached Docker containers.
If Docker cleanup is requested, first identify the exact Compose project and
use an explicit, scoped Docker operation; never stop unrelated containers or
the Docker daemon.

### Reset a configured launch

Reset replaces the terminal for the configured process and replays its complete
saved command sequence from the original working directory. This reruns commands
and their side effects; the replacement has a new session ID and does not keep
the old shell state or output. Other terminals are unaffected, and detached
Docker containers are not stopped.

```powershell
# From a managed configured terminal:
dockerino reset

# From an external CLI:
dotnet @cli session reset $sessionId
```

Reset requires the application to be running and does not start it. The CLI
response confirms request acceptance, not successful completion. Inspect the
associated launch with `launch inspect` to check the pending, succeeded, or
failed reset outcome. The terminal-context shorthand is not supported in an
independent **New terminal**. A self-reset can close its invoking terminal and
CLI before the response is displayed.

## Command reference

```text
list
get <project-id>
register <name> <absolute-directory>
validate <json-file>
save <json-file> [expected-revision]
git fetch|pull <project-id> <repository-path> [repository-path ...]
projects refresh [project-id]
launch start <project-id> <action-id>
launch list
launch inspect|stop <launch-id>
session list
session inspect|stop|reset|output|follow <session-id> [--offset <offset>]
reset
dashboard open
--help
--version
```

For implementation status and usage details, consult the repository
[`README.md`](../../../README.md).

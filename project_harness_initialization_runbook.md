# Project Harness Initialization Runbook

> This file is intended to be given directly to Codex, Claude Code, or another coding agent.
> Follow it as an execution protocol for initializing a project harness in the current repository.
> Do not treat this as background reading. Treat it as the task specification.

---

## 0. Mission

Initialize a lightweight but complete Project Harness for the current repository.

The harness must make future agent work:

- resumable across sessions
- verifiable before completion
- recoverable after failures
- auditable through logs and state files
- constrained by explicit project rules

The goal is not to add documentation for its own sake.  
The goal is to create a stable engineering execution environment around an unreliable language model.

Do not implement business features during harness initialization.

---

## 1. Core Principles

### 1.1 Reliable system, unreliable model

Do not rely on the model remembering rules from chat history.

Use different carriers for different kinds of control:

- Prompt: reminders
- Markdown: protocols and project rules
- State files: session continuity
- Scripts/hooks: executable boundaries
- Tests/checks: completion evidence

### 1.2 Context is a project asset, not chat history

Anything needed by future sessions must be written into project files.

Persist at least:

- current task
- current status
- active plan
- important decisions
- known failure modes
- validation commands
- next steps

### 1.3 Read protocol before acting

At the start of each future session, the agent must recover context before editing code.

Required reading order:

1. `AGENTS.md`
2. `current-task.md`
3. `.harness/session-state.json`
4. `.harness/session-log.md`
5. `docs/decisions.md`
6. `docs/error-journal.md`
7. `docs/verification.md`

After reading, output a short Session Briefing before doing work.

### 1.4 Completion requires verification

The agent may not declare a code task complete only because the implementation “looks right”.

A task is complete only after:

1. relevant validation commands were run, or
2. validation could not be run and the risk is explicitly recorded.

### 1.5 Every session ends with handoff

At the end of a session, update:

- `current-task.md`
- `.harness/session-state.json`
- `.harness/session-log.md`

If relevant, also update:

- `docs/decisions.md`
- `docs/error-journal.md`
- `.harness/progress-map.md`
- `.harness/command-history.md`

---

## 2. Harness Layers

Create the harness as five layers.

### 2.1 Boundary Layer

Purpose: turn “do not do this” into executable or semi-executable guardrails.

Files:

- `scripts/harness_check.sh`
- `scripts/safe_bash_guard.sh`
- optional: `.git/hooks/pre-commit`

Responsibilities:

- detect missing harness files
- block known dangerous commands
- discourage unplanned large edits
- require validation before completion

### 2.2 Knowledge Layer

Purpose: store stable project knowledge.

Files:

- `docs/architecture.md`
- `docs/verification.md`
- `docs/coding-guidelines.md`
- `docs/decisions.md`
- `docs/error-journal.md`

Responsibilities:

- describe project structure
- describe validation strategy
- record design decisions
- record repeated failures and fixes
- avoid relying on chat memory

### 2.3 State Layer

Purpose: preserve active task state across sessions.

Files:

- `current-task.md`
- `.harness/session-state.json`
- `.harness/session-log.md`
- `.harness/progress-map.md`
- `.harness/command-history.md`

Responsibilities:

- define the current goal
- record current phase
- record active plan
- record changed files
- record validation results
- define next steps

### 2.4 Verification Layer

Purpose: define what “done” means.

Sources:

- existing test scripts
- package manager commands
- Makefile targets
- CI config
- simulation/synthesis scripts
- local smoke checks

Responsibilities:

- map change types to validation commands
- document unavailable validation
- record known failures
- prevent unverifiable completion claims

### 2.5 Skill Layer

Purpose: package recurring workflows into reusable agent procedures.

Files:

- `skills/start/SKILL.md`
- `skills/plan/SKILL.md`
- `skills/review/SKILL.md`
- `skills/commit/SKILL.md`
- `skills/handoff/SKILL.md`

Responsibilities:

- standardize session startup
- standardize planning
- standardize review
- standardize pre-commit validation
- standardize handoff

---

## 3. Initialization Scope

Choose one initialization level based on repository risk.

### 3.1 Light Harness

Use for:

- small demos
- one-off scripts
- documentation-only repositories
- short-lived experiments

Create:

```text
AGENTS.md
current-task.md
docs/verification.md
.harness/session-state.json
.harness/session-log.md
skills/start/SKILL.md
skills/handoff/SKILL.md
scripts/harness_check.sh
```

### 3.2 Standard Harness

Use for:

- normal software repositories
- long-term projects
- repositories where agents will make repeated changes

Create everything in Light Harness plus:

```text
docs/architecture.md
docs/coding-guidelines.md
docs/decisions.md
docs/error-journal.md
.harness/progress-map.md
.harness/command-history.md
skills/plan/SKILL.md
skills/review/SKILL.md
skills/commit/SKILL.md
scripts/safe_bash_guard.sh
```

### 3.3 Full Harness

Use for:

- production repositories
- multi-agent workflows
- EDA / RTL repositories
- database projects
- deployment projects
- cloud infrastructure projects
- projects with destructive or expensive operations

Create everything in Standard Harness plus project-specific additions such as:

```text
docs/verification-matrix.md
docs/approval-policy.md
.git/hooks/pre-commit
ci/harness-check.yml
```

Default to Standard Harness unless the repository clearly fits Light or Full.

---

## 4. Required Final Directory Structure

For Standard Harness, create this structure:

```text
.
├── AGENTS.md
├── current-task.md
├── docs/
│   ├── architecture.md
│   ├── verification.md
│   ├── coding-guidelines.md
│   ├── decisions.md
│   └── error-journal.md
├── .harness/
│   ├── session-state.json
│   ├── session-log.md
│   ├── progress-map.md
│   └── command-history.md
├── skills/
│   ├── start/
│   │   └── SKILL.md
│   ├── plan/
│   │   └── SKILL.md
│   ├── review/
│   │   └── SKILL.md
│   ├── commit/
│   │   └── SKILL.md
│   └── handoff/
│       └── SKILL.md
└── scripts/
    ├── harness_check.sh
    └── safe_bash_guard.sh
```

Do not modify business logic files during initialization unless explicitly instructed.

Avoid modifying:

```text
src/*
app/*
lib/*
core/*
production config
database migrations
deployment scripts
```

Allowed initialization targets:

```text
AGENTS.md
current-task.md
docs/*
.harness/*
skills/*
scripts/harness_check.sh
scripts/safe_bash_guard.sh
```

---

## 5. Execution Phases

Follow these phases in order.

---

### Phase 1: Repository Reconnaissance

Read only. Do not modify files.

Run safe inspection commands:

```bash
pwd
ls
find . -maxdepth 2 -type f | sort | head -200
git status --short
```

Identify project type using signals:

```text
package.json        -> Node / TypeScript / JavaScript / Bun
pyproject.toml      -> Python
requirements.txt    -> Python
Cargo.toml          -> Rust
go.mod              -> Go
Makefile            -> Make / C / C++ / RTL / generic build
*.v or *.sv         -> RTL / Verilog / SystemVerilog
docker-compose.yml  -> service / deployment project
.github/workflows   -> CI-enabled repository
```

Output a short reconnaissance summary:

```text
Project Type:
Main Directories:
Existing Validation:
Existing Docs:
Risk Level:
Selected Harness Level:
Notes:
```

---

### Phase 2: Create Harness Skeleton

Create directories:

```bash
mkdir -p docs .harness skills/{start,plan,review,commit,handoff} scripts
```

Create or update files:

```text
AGENTS.md
current-task.md
docs/architecture.md
docs/verification.md
docs/coding-guidelines.md
docs/decisions.md
docs/error-journal.md
.harness/session-state.json
.harness/session-log.md
.harness/progress-map.md
.harness/command-history.md
skills/start/SKILL.md
skills/plan/SKILL.md
skills/review/SKILL.md
skills/commit/SKILL.md
skills/handoff/SKILL.md
scripts/harness_check.sh
scripts/safe_bash_guard.sh
```

If a file already exists, preserve useful existing content and append/update rather than blindly overwrite.

---

### Phase 3: Write `AGENTS.md`

`AGENTS.md` must be concise. It is an index and operating protocol, not a full encyclopedia.

Use this structure:

```markdown
# Agent Operating Guide

## Role

You are an engineering agent working inside this repository.

Your job is to make scoped, verifiable changes while preserving project continuity across sessions.

## Required Reading Order

Before editing code, read:

1. `current-task.md`
2. `.harness/session-state.json`
3. `.harness/session-log.md`
4. `docs/verification.md`
5. `docs/decisions.md`
6. `docs/error-journal.md`

Then output a short Session Briefing.

## Core Rules

- Do not rely on chat history for project state.
- Do not make broad unplanned edits.
- Do not modify business logic during harness initialization.
- Keep changes scoped to the active task.
- Prefer small, reviewable diffs.
- Record important decisions in `docs/decisions.md`.
- Record repeated failures in `docs/error-journal.md`.

## Planning Rules

Before non-trivial edits:

1. inspect relevant files
2. write or update the plan
3. identify validation commands
4. then implement

## Verification Policy

Completion requires validation evidence.

Use `docs/verification.md` to choose validation commands.

If validation cannot be run, record:

- what was not run
- why it was not run
- expected risk
- recommended follow-up

## Safety Policy

Do not run destructive commands unless explicitly requested.

Use `scripts/safe_bash_guard.sh` when evaluating risky shell commands.

## Handoff Policy

Before ending a session, update:

- `current-task.md`
- `.harness/session-state.json`
- `.harness/session-log.md`

## Recommended Skills

- `/start`: recover context
- `/plan`: create/update implementation plan
- `/review`: review current diff
- `/commit`: validate and prepare commit summary
- `/handoff`: preserve session state

## Output Style

- Be concise.
- State assumptions.
- Report files changed.
- Report validation commands and results.
- Do not claim completion without evidence.
```

---

### Phase 4: Write `current-task.md`

Create a human-readable task state file.

Template:

````markdown
# Current Task

## Goal

Initialize a Project Harness for this repository.

## Current Status

Harness initialization in progress.

## Scope

Allowed:

- create/update `AGENTS.md`
- create/update `current-task.md`
- create/update `docs/*`
- create/update `.harness/*`
- create/update `skills/*/SKILL.md`
- create/update `scripts/harness_check.sh`
- create/update `scripts/safe_bash_guard.sh`

Not allowed unless explicitly requested:

- business logic changes
- production configuration changes
- database migrations
- deployment changes
- unrelated refactors

## Relevant Files

- `AGENTS.md`
- `docs/verification.md`
- `docs/decisions.md`
- `docs/error-journal.md`
- `.harness/session-state.json`
- `.harness/session-log.md`

## Plan

1. Inspect repository structure.
2. Select harness level.
3. Create harness files.
4. Write operating protocol.
5. Write validation protocol.
6. Write skills.
7. Write safety/check scripts.
8. Run harness self-check.
9. Record handoff.

## Validation Commands

```bash
bash scripts/harness_check.sh
```

Additional project-specific validation commands should be added to `docs/verification.md`.

## Acceptance Criteria

- Required harness files exist.
- `AGENTS.md` defines agent operating rules.
- `docs/verification.md` explains validation strategy.
- `.harness/session-state.json` is valid JSON.
- `/start` and `/handoff` skills exist.
- `scripts/harness_check.sh` passes.
- No business logic files are modified.

## Risks

- Existing project validation commands may be unclear.
- Existing conventions may be incomplete.
- Hooks may not be installed automatically.

## Next 3 Steps

1. Run `bash scripts/harness_check.sh`.
2. Review generated harness files.
3. Start future work with `/start`.

## Last Updated

Replace with current date/time.
````

---

### Phase 5: Write `.harness/session-state.json`

Create valid JSON:

```json
{
  "task_id": "harness-initialization",
  "status": "in_progress",
  "current_phase": "initialization",
  "last_updated": "",
  "active_plan": [
    "Inspect repository",
    "Create harness files",
    "Write protocols",
    "Run harness check",
    "Record handoff"
  ],
  "next_3_steps": [
    "Run harness self-check",
    "Review generated files",
    "Use /start in the next session"
  ],
  "changed_files": [],
  "validation": {
    "last_commands": [],
    "last_result": "not_run",
    "known_failures": []
  },
  "cognitive_state": {
    "failure_count": 0,
    "current_mode": "initialization",
    "tried_approaches": [],
    "blocked_on": null
  },
  "handoff": {
    "last_summary": "",
    "resume_from": "Run /start, then inspect current-task.md and docs/verification.md."
  }
}
```

Update this file after the harness check.

---

### Phase 6: Write `.harness/session-log.md`

Template:

````markdown
# Session Log

## Entry: Harness Initialization

### Summary

Initialized project harness structure.

### Files Created or Updated

- `AGENTS.md`
- `current-task.md`
- `docs/*`
- `.harness/*`
- `skills/*/SKILL.md`
- `scripts/*`

### Validation

Pending: `bash scripts/harness_check.sh`

### Next Steps

1. Run `/start` in the next agent session.
2. Confirm validation commands in `docs/verification.md`.
3. Begin implementation work only after planning.
````

---

### Phase 7: Write `docs/architecture.md`

If the repository structure is clear, write a short factual overview.

Do not invent architecture.

Template:

```markdown
# Architecture Notes

## Repository Type

To be determined from repository inspection.

## Main Directories

List observed directories and their likely responsibilities.

## Entry Points

List discovered application, package, CLI, test, or build entry points.

## Data / Control Flow

To be expanded when the project structure is better understood.

## Notes for Future Agents

- Do not assume architecture from file names alone.
- Inspect relevant files before editing.
- Record stable architecture decisions in `docs/decisions.md`.
```

---

### Phase 8: Write `docs/verification.md`

Choose commands based on detected project type.

Use this template and fill detected commands only when evidence exists:

````markdown
# Verification Guide

## Purpose

This file defines how agents should verify changes before declaring work complete.

## Baseline Harness Check

Always available:

```bash
bash scripts/harness_check.sh
```

## Project-Specific Validation

### Node / TypeScript / JavaScript / Bun

Use only if corresponding scripts exist:

```bash
bun test
bun run typecheck
bun run lint
npm test
npm run typecheck
npm run lint
```

### Python

Use only if dependencies/configs exist:

```bash
pytest
ruff check .
mypy .
```

### Rust

```bash
cargo test
cargo clippy
cargo fmt --check
```

### Go

```bash
go test ./...
go vet ./...
```


## Change-Type Validation Matrix

| Change Type | Required Validation |
|---|---|
| Documentation only | `bash scripts/harness_check.sh` |
| Harness files | `bash scripts/harness_check.sh` |
| Code logic | project tests + relevant lint/typecheck |
| API/interface | tests + affected integration checks |
| RTL logic | lint + simulation if available |
| Build/deployment | build command + smoke check |

## If Validation Cannot Be Run

Record in `.harness/session-log.md`:

- command not run
- reason
- risk
- recommended follow-up

Do not claim full completion without validation evidence.
````

---

### Phase 9: Write `docs/coding-guidelines.md`

Do not invent detailed conventions. Record only observed or minimal conventions.

Template:

````markdown
# Coding Guidelines

## General Rules

- Keep changes small and scoped.
- Prefer existing project style over new style.
- Do not introduce new dependencies without explicit reason.
- Do not perform unrelated refactors.
- Update tests or validation notes when behavior changes.

## Language-Specific Notes

Fill this section only after inspecting the repository.

## Agent Notes

- Inspect nearby files before editing.
- Match naming, formatting, and error-handling style already present.
- If conventions are unclear, state assumptions before editing.
````

---

### Phase 10: Write `docs/decisions.md`

Template:

```markdown
# Decision Log

Use this file to record stable project decisions.

## Format

```markdown
## YYYY-MM-DD - Decision Title

### Context

What problem or constraint led to this decision?

### Decision

What was decided?

### Alternatives Considered

What alternatives were considered?

### Consequences

What tradeoffs or future implications does this create?


## Decisions

No major project decisions recorded yet.
```

---

### Phase 11: Write `docs/error-journal.md`

Template:

```markdown
# Error Journal

Use this file to record repeated failures, non-obvious bugs, and lessons learned.

## Format

```markdown
## YYYY-MM-DD - Error Title

### Symptom

What went wrong?

### Root Cause

Why did it happen?

### Fix

How was it fixed?

### Prevention

How should future agents avoid repeating it?

### Related Files

- `path/to/file`

## Known Failure Modes

No failure modes recorded yet.
```

---

### Phase 12: Write Skills

Each skill must be a `SKILL.md` file.

Use concise, actionable instructions.

---

#### `skills/start/SKILL.md`

````markdown
---
name: start
description: Recover project context at the beginning of a new agent session.
---

# Start Skill

## Goal

Recover project context before making changes.

## Trigger

Use when starting a new session or before beginning work in this repository.

## Rules

- Do not edit code during `/start`.
- Read required files first.
- Output a Session Briefing.
- If files are missing, report them and recommend running `bash scripts/harness_check.sh`.

## Steps

1. Read `AGENTS.md`.
2. Read `current-task.md`.
3. Read `.harness/session-state.json`.
4. Read `.harness/session-log.md`.
5. Read `docs/verification.md`.
6. Read `docs/decisions.md`.
7. Read `docs/error-journal.md`.
8. Output Session Briefing.

## Output Format

```text
Session Briefing

Current Goal:
Current Status:
Current Phase:
Next 3 Steps:
Relevant Files:
Validation Commands:
Known Risks:
Questions / Blockers:
```

## Completion Criteria

- Session context is summarized.
- No code was modified.
- Next action is clear.
```

---

#### `skills/plan/SKILL.md`

```markdown
---
name: plan
description: Convert a user request into a scoped engineering plan and update harness state.
---

# Plan Skill

## Goal

Turn a request into a concrete, scoped, verifiable plan.

## Trigger

Use before non-trivial implementation, multi-file changes, refactors, or uncertain tasks.

## Rules

- Inspect relevant files before planning.
- Keep the plan scoped.
- Include validation commands.
- Update `current-task.md` and `.harness/session-state.json`.

## Steps

1. Restate the user request.
2. Inspect relevant files.
3. Identify scope and non-scope.
4. Create implementation plan.
5. Identify validation commands.
6. Update harness state.

## Output Format

```text
Plan

Goal:
Scope:
Non-Scope:
Files Likely Affected:
Steps:
Validation:
Risks:
```

## Completion Criteria

- Plan is written.
- Validation path is identified.
- Harness state is updated.
```

---

#### `skills/review/SKILL.md`

```markdown
---
name: review
description: Review current changes for correctness, scope control, and validation readiness.
---

# Review Skill

## Goal

Review the current diff before completion or commit.

## Trigger

Use after implementation and before `/commit` or `/handoff`.

## Rules

- Review actual diff.
- Classify findings as BLOCKER, MAJOR, MINOR, or QUESTION.
- Check whether validation was run.
- Do not make unrelated changes.

## Steps

1. Inspect `git status --short`.
2. Inspect relevant diff.
3. Check against current task scope.
4. Check validation evidence.
5. Report findings.

## Output Format

```text
Review Result

BLOCKER:
MAJOR:
MINOR:
QUESTION:
Validation Status:
Recommended Fixes:
```

## Completion Criteria

- Findings are classified.
- Scope drift is identified.
- Validation gaps are identified.
```

---

#### `skills/commit/SKILL.md`

```markdown
---
name: commit
description: Prepare a validated commit summary without committing unless explicitly requested.
---

# Commit Skill

## Goal

Prepare changes for commit after validation.

## Trigger

Use when the implementation is complete and ready for final validation.

## Rules

- Do not run `git commit` unless the user explicitly asks.
- Inspect diff before preparing commit message.
- Run or confirm validation commands.
- Update `.harness/session-log.md`.

## Steps

1. Run `git status --short`.
2. Inspect diff summary.
3. Run relevant validation commands.
4. Update session log with validation result.
5. Generate commit message.

## Output Format

```text
Commit Preparation

Files Changed:
Validation Commands:
Validation Result:
Commit Message:
Risks / Notes:
```

## Completion Criteria

- Diff is reviewed.
- Validation result is recorded.
- Commit message is ready.
```

---

#### `skills/handoff/SKILL.md`

```markdown
---
name: handoff
description: Preserve session state so the next agent session can resume without chat history.
---

# Handoff Skill

## Goal

Close the current session by writing durable state.

## Trigger

Use at the end of a session, after a major step, or before context may be lost.

## Rules

- Update state files.
- Record what changed.
- Record validation status.
- Record next steps.
- Record unresolved risks.
- Do not claim completion without validation evidence.

## Steps

1. Inspect `git status --short`.
2. Summarize completed work.
3. Summarize changed files.
4. Summarize validation commands and results.
5. Update `current-task.md`.
6. Update `.harness/session-state.json`.
7. Append to `.harness/session-log.md`.
8. Update `docs/decisions.md` or `docs/error-journal.md` if needed.

## Output Format

```text
Handoff Summary

Completed:
Changed Files:
Validation:
Known Issues:
Next 3 Steps:
Resume From:
```

## Completion Criteria

- State files are updated.
- Next session can resume from `/start`.
- Validation status is explicit.
````

---

### Phase 13: Write `scripts/harness_check.sh`

Create an executable shell script:

```bash
#!/usr/bin/env bash
set -euo pipefail

required_files=(
  "AGENTS.md"
  "current-task.md"
  "docs/verification.md"
  ".harness/session-state.json"
  ".harness/session-log.md"
  "skills/start/SKILL.md"
  "skills/handoff/SKILL.md"
  "scripts/harness_check.sh"
)

missing=0

for file in "${required_files[@]}"; do
  if [[ ! -f "$file" ]]; then
    echo "MISSING: $file"
    missing=1
  fi
done

if [[ -f ".harness/session-state.json" ]]; then
  if command -v python3 >/dev/null 2>&1; then
    python3 -m json.tool .harness/session-state.json >/dev/null
  elif command -v python >/dev/null 2>&1; then
    python -m json.tool .harness/session-state.json >/dev/null
  else
    echo "WARN: python not found; skipping JSON validation"
  fi
fi

if [[ "$missing" -ne 0 ]]; then
  echo "Harness check failed."
  exit 1
fi

echo "Harness check passed."
```

Then run:

```bash
chmod +x scripts/harness_check.sh
```

---

### Phase 14: Write `scripts/safe_bash_guard.sh`

Create an executable shell script:

```bash
#!/usr/bin/env bash
set -euo pipefail

cmd="${*:-}"

if [[ -z "$cmd" ]]; then
  echo "Usage: $0 <command string>"
  exit 2
fi

dangerous_patterns=(
  "rm -rf /"
  "rm -rf ."
  "git reset --hard"
  "git clean -fd"
  "git push --force"
  "drop database"
  "truncate table"
  "supabase db reset"
  "prisma migrate reset"
)

lower_cmd="$(printf '%s' "$cmd" | tr '[:upper:]' '[:lower:]')"

for pattern in "${dangerous_patterns[@]}"; do
  if [[ "$lower_cmd" == *"$pattern"* ]]; then
    echo "BLOCKED: dangerous command pattern detected: $pattern"
    echo "Human confirmation is required before running this command."
    exit 1
  fi
done

echo "Command passed safe_bash_guard."
```

Then run:

```bash
chmod +x scripts/safe_bash_guard.sh
```

---

### Phase 15: Self-Check

Run:

```bash
bash scripts/harness_check.sh
```

Update:

- `current-task.md`
- `.harness/session-state.json`
- `.harness/session-log.md`

The final state must include:

```text
validation.last_commands = ["bash scripts/harness_check.sh"]
validation.last_result = "passed" or "failed"
handoff.resume_from = "Run /start, then continue from current-task.md."
```

---

## 6. Final Report Format

After initialization, output:

```text
Harness Initialization Complete

Project Type:
Harness Level:
Files Created/Updated:
Validation:
- Command:
- Result:

Business Logic Modified:
- No

How to Use Next:
1. Start a new session with /start.
2. Use /plan for the next engineering task.
3. Use /handoff before ending the session.

Notes / Risks:
```

If something failed, output:

```text
Harness Initialization Incomplete

Completed:
Failed:
Reason:
Files Created/Updated:
Recommended Fix:
```

---

## 7. Quality Bar

A valid harness initialization must satisfy all of these:

1. A new session can recover context by running `/start`.
2. `current-task.md` states goal, scope, plan, validation, acceptance criteria, and next steps.
3. `.harness/session-state.json` is valid JSON.
4. `docs/verification.md` explains how to verify work.
5. `docs/decisions.md` exists for stable decisions.
6. `docs/error-journal.md` exists for failure patterns.
7. `/handoff` can preserve state for the next session.
8. At least one executable check exists: `scripts/harness_check.sh`.
9. Safety boundary exists: `scripts/safe_bash_guard.sh`.
10. Initialization does not change business logic.

---

## 8. Mental Model

The harness should make the project behave like this:

```text
start session
  -> read protocol
  -> recover state
  -> produce briefing
  -> plan
  -> implement scoped change
  -> validate
  -> record result
  -> handoff
  -> next session resumes without chat history
```

The core idea:

> Build protocol, state, validation, boundaries, and handoff first. Then let the agent write code.

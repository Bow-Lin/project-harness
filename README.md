# Project Harness

> TLDR: 使用方法很简单：直接把 `project_harness_initialization_runbook.md` 的内容复制给 Codex、Claude Code 或其他 coding agent，并要求它“按照这个文件在当前仓库执行”。
>
> TLDR: To use this project, copy the contents of `project_harness_initialization_runbook.md` directly into Codex, Claude Code, or another coding agent, then ask it to "execute this file in the current repository."

轻量但完整的工程执行护栏，用来帮助 Codex、Claude Code 或其他 coding agent 在一个仓库中更可靠地工作。

A lightweight but complete engineering harness that helps Codex, Claude Code, or other coding agents work more reliably inside a repository.

## 概览 / Overview

Project Harness 是一份面向 coding agent 的初始化运行手册。它不是业务框架，也不是代码生成器；它的目标是在现有仓库周围建立一层可恢复、可验证、可审计的工程执行环境。

Project Harness is an initialization runbook for coding agents. It is not an application framework or a code generator. Its goal is to build a resumable, verifiable, and auditable engineering execution environment around an existing repository.

核心文件是：

The core file is:

- `project_harness_initialization_runbook.md`

这份 runbook 可以直接交给 Codex、Claude Code 或类似 coding agent，作为初始化 Project Harness 的执行协议。

This runbook can be given directly to Codex, Claude Code, or a similar coding agent as the execution protocol for initializing a Project Harness.

## 为什么需要它 / Why This Exists

语言模型很擅长写代码，但不应该依赖聊天记录来记住项目规则、当前任务、验证方式和失败经验。Project Harness 把这些关键信息写入仓库文件，让未来的 agent session 可以从项目状态中恢复，而不是从不可靠的上下文记忆中猜测。

Language models can be good at writing code, but they should not rely on chat history to remember project rules, active tasks, validation commands, or lessons from failures. Project Harness stores that information in repository files so future agent sessions can recover from project state instead of guessing from fragile context memory.

它重点解决：

It focuses on:

- 跨 session 恢复上下文 / Resuming context across sessions
- 完成前必须验证 / Requiring verification before completion
- 失败后可恢复 / Recovering after failures
- 通过日志和状态文件审计过程 / Auditing work through logs and state files
- 用明确项目规则约束 agent 行为 / Constraining agent behavior with explicit project rules

## 它会创建什么 / What It Creates

默认情况下，runbook 会为普通软件项目初始化一个 Standard Harness，包括：

By default, the runbook initializes a Standard Harness for normal software repositories, including:

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

这些文件分别承担项目规则、当前任务、验证说明、决策记录、错误记录、session 状态、agent 技能和安全检查等职责。

These files cover project rules, the active task, verification guidance, decision records, error records, session state, reusable agent skills, and safety checks.

## 快速开始 / Quick Start

1. 将 `project_harness_initialization_runbook.md` 放在目标仓库中，或让 coding agent 读取该文件。
2. 告诉 agent：按照这份 runbook 在当前仓库初始化 Project Harness。
3. 初始化完成后，运行 harness 自检：

1. Place `project_harness_initialization_runbook.md` in the target repository, or ask the coding agent to read it.
2. Tell the agent to initialize a Project Harness in the current repository by following the runbook.
3. After initialization, run the harness self-check:

```bash
bash scripts/harness_check.sh
```

之后的 agent session 应先执行 `/start` 恢复上下文，进行非平凡修改前使用 `/plan`，结束前使用 `/handoff` 写入交接状态。

Future agent sessions should start with `/start` to recover context, use `/plan` before non-trivial changes, and use `/handoff` before ending the session.

## Harness 级别 / Harness Levels

runbook 定义了三个初始化级别：

The runbook defines three initialization levels:

- **Light Harness**：适合小 demo、一次性脚本、纯文档仓库或短期实验。
- **Standard Harness**：适合普通软件仓库、长期项目和会反复使用 agent 的项目。默认选择。
- **Full Harness**：适合生产仓库、多 agent 工作流、EDA/RTL、数据库、部署或云基础设施项目。

- **Light Harness**: For small demos, one-off scripts, documentation-only repositories, or short-lived experiments.
- **Standard Harness**: For normal software repositories, long-term projects, and repositories where agents will make repeated changes. This is the default.
- **Full Harness**: For production repositories, multi-agent workflows, EDA/RTL, database projects, deployment projects, or cloud infrastructure.

## 验证与安全 / Verification and Safety

Project Harness 的核心原则是：不能只因为实现“看起来正确”就宣布完成。完成必须有验证证据，或者明确记录为什么无法验证以及对应风险。

One core principle of Project Harness is that work is not complete just because the implementation "looks right." Completion requires validation evidence, or an explicit record of why validation could not be run and what risk remains.

初始化后至少会提供：

After initialization, the harness provides at least:

- `scripts/harness_check.sh`：检查关键 harness 文件是否存在，并验证 `.harness/session-state.json` 是合法 JSON。
- `scripts/safe_bash_guard.sh`：阻止或警告已知危险命令模式。
- `docs/verification.md`：记录不同变更类型应运行的验证命令。
- `.harness/session-log.md`：记录 session 过程、验证结果和交接信息。

- `scripts/harness_check.sh`: Checks that required harness files exist and validates `.harness/session-state.json` as JSON.
- `scripts/safe_bash_guard.sh`: Blocks or warns about known dangerous command patterns.
- `docs/verification.md`: Records validation commands for different change types.
- `.harness/session-log.md`: Records session progress, validation results, and handoff notes.

## 仓库内容 / Repository Contents

当前仓库包含：

This repository currently contains:

- `project_harness_initialization_runbook.md`：Project Harness 初始化协议。
- `LICENSE`：MIT 许可证。

- `project_harness_initialization_runbook.md`: The Project Harness initialization protocol.
- `LICENSE`: MIT License.

## 适用对象 / Intended Users

Project Harness 适合希望让 coding agent 在真实仓库中更稳定工作的开发者，尤其适合需要多轮 session、可验证交付、失败复盘和项目规则持久化的场景。

Project Harness is for developers who want coding agents to work more reliably in real repositories, especially when work spans multiple sessions, requires verifiable delivery, needs failure recovery, or depends on persistent project rules.

## 许可证 / License

本项目使用 MIT License。

This project is licensed under the MIT License.

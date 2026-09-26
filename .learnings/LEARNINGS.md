# Learnings

Corrections, insights, and knowledge gaps captured during development.

**Categories**: correction | insight | knowledge_gap | best_practice
**Areas**: frontend | backend | infra | tests | docs | config
**Statuses**: pending | in_progress | resolved | wont_fix | promoted | promoted_to_skill

## Status Definitions

| Status | Meaning |
|--------|---------|
| `pending` | Not yet addressed |
| `in_progress` | Actively being worked on |
| `resolved` | Issue fixed or knowledge integrated |
| `wont_fix` | Decided not to address (reason in Resolution) |
| `promoted` | Elevated to SOUL.md, TOOLS.md, or AGENTS.md |
| `promoted_to_skill` | Extracted as a reusable skill |

## Skill Extraction Fields

When a learning is promoted to a skill, add these fields:

```markdown
**Status**: promoted_to_skill
**Skill-Path**: skills/skill-name
```

Example:
```markdown
## [LRN-20250115-001] best_practice

**Logged**: 2025-01-15T10:00:00Z
**Priority**: high
**Status**: promoted_to_skill
**Skill-Path**: skills/docker-m1-fixes
**Area**: infra

### Summary
Docker build fails on Apple Silicon due to platform mismatch
...

---

## [LRN-20260304-001] correction

**Logged**: 2026-03-04T00:00:00Z
**Priority**: high
**Status**: resolved
**Area**: config

### Summary
`.workflow/` 曾引入 `runtimes.json` 与旧 schema 的 `agents.json`，两个文件都不是当前工作流的维度

### Details
旧 `.workflow/` 里有一份 `runtimes.json`，登记 runtime / adapter / phase routing；`agents.json` 写成 `schema_version: 2`，字段是 `role`、`cost_tier`，没有 `harness`、`host`、`strength`、`speed`。当前工作流里：

- 执行方式只有一种，每个 ticket 一个 git worktree。没有 runtime、adapter、队列、面板这些维度
- `agents.json` 是唯一注册表，只描述 agent 本身：harness、model、host、strength、speed、tags
- 本机和服务器能力相同，差别只在各自装了哪些 agent

所以 `runtimes.json` 整份属于废弃维度，`agents.json` 的旧 schema 撑不住 `/route-agent` 的档位判断。

### Suggested Action
删除 `runtimes.json`；`agents.json` 回到 `schema_version: 1` 的基线名单结构，补上 `harness`、`model`、`host`、`strength`、`speed`。之后改 agent 走 `/setup-agents`。

### Metadata
- Source: user_feedback
- Related Files: .workflow/runtimes.json, .workflow/agents.json
- Tags: workflow, config, agents-registry, deprecated-dimension
- Pattern-Key: workflow.deprecated-runtime-dimension
- Recurrence-Count: 1
- First-Seen: 2026-03-04
- Last-Seen: 2026-03-04

---

## [LRN-20260304-002] knowledge_gap

**Logged**: 2026-03-04T00:00:00Z
**Priority**: medium
**Status**: resolved
**Area**: config

### Summary
`config.json` 漏了 `worktree_dir`，项目根也没有 `.gitignore`

### Details
`/dispatch` 按 `config.json` 的 `worktree_dir` 建每个 ticket 的工作树。旧 `config.json` 八个字段里没有这一项，项目根也没有 `.gitignore`，`.worktrees/` 一旦建出来就会被 git 当成待提交文件。

### Suggested Action
`config.json` 补 `worktree_dir: ".worktrees"`；项目根建 `.gitignore` 并写入 `.worktrees/`。本次用户选择暂不处理 `.gitignore`，需要时补上。

### Metadata
- Source: conversation
- Related Files: .workflow/config.json
- Tags: workflow, config, gitignore, worktree
- Pattern-Key: config.missing-worktree-dir
- Recurrence-Count: 1
- First-Seen: 2026-03-04

---

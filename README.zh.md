# spec-forge

一个 SDD 风格的技能仓库，默认工作流：**`sdd-feature` → `sdd-plan` → `sdd-build` → `sdd-review` → `sdd-release`**（按需 **`sdd-subagent-build`**、**`sdd-simplify`**、**`sdd-init`**、**`sdd-brainstorm`**）。

操作模型：

- 按需发现：**`sdd-init`**；方向仍模糊时用 **`sdd-brainstorm`**
- 冻结工作：**`sdd-feature`**，再 **`sdd-plan`**
- 执行计划：**`sdd-build`**；独立任务交给 **`sdd-subagent-build`**，仅在文件归属安全、计划允许时再并行
- 验证结果：**`sdd-review`**，只审不改：**五轴**合并就绪评审、**五步**流程，分级 **🔴 / 🟠 / 🟡 / 🔵**（见 **`skills/sdd-review/SKILL.md`** Step 4）
- 按需精简：**`sdd-simplify`**；改动不小则再跑 **`sdd-review`**
- 交付结果：**`sdd-release`**

English version: [README.md](./README.md)

## Cursor 插件

本仓库提供 **Cursor 插件清单**：

| 路径                               | 作用                                                           |
|----------------------------------|--------------------------------------------------------------|
| **`.cursor-plugin/plugin.json`** | 声明 **`skills/`**、**`agents/`**、**`hooks/hooks-cursor.json`** |
| **`hooks/session-start`**        | 在新 Agent 会话注入 **`skills/use-sdd/SKILL.md`**（需安装 **`jq`**）    |

安装方式：

- **本机插件：** 将仓库放入或链到 **`~/.cursor/plugins/local/spec-forge/`**（**`.cursor-plugin/plugin.json`** 须在插件根目录）。例：
  `ln -s /你的路径/spec-forge ~/.cursor/plugins/local/spec-forge`，然后执行 **Developer: Reload Window** 或重启
  Cursor。若软链不生效可改用 **`cp -R`**（个别版本对软链有问题）。
- **远程：** **设置 → Rules → Add Rule → Remote (GitHub)** 填本仓库或你的 fork。

安装后技能在 **`skills/*/SKILL.md`**，钩子由清单中的 **`hooks/hooks-cursor.json`** 驱动。

## 技能指南

### `sdd-init`

- **作用：** 基于仓库事实创建或刷新 `PROJECT.md`。
- **上下文：** 仓库文件、约定、用户约束。
- **结果：** 下游可信任的确定性事实文件。
- **流程：** 自动识别已说清楚的部分，再对缺口做一轮分组补问。

### `sdd-brainstorm`

- **作用：** 把模糊想法收敛成可冻结的方向。
- **上下文：** 粗糙目标、问题或不完整请求。
- **结果：** 更窄方向、假设、风险、最小阻塞问题（如需）。
- **流程：** 先查文档/代码，再最少补问以解锁 **`sdd-feature`**。

### `sdd-feature`

- **作用：** 冻结范围与可观察需求。
- **上下文：** 有边界的目标、`PROJECT.md`、可选 brainstorm 结论。
- **产物：** `docs/features/<feature-slug>.md`（稳定需求标题，供 **`sdd-plan` `Covers:`** 逐字引用）。
- **流程：** 边界 + 非目标 + 可观察行为—别让实现层名词渗进需求。

### `sdd-plan`

- **作用：** 把冻结后的 feature 落成可执行计划。
- **上下文：** `docs/features/<feature-slug>.md`、项目事实。
- **产物：** `docs/plans/<yyyy-mm-dd-feature-slug>.md` — **先文件边界**、依赖顺序、纵向切片、**Checkpoint**、可选 **并行泳道**、文末 **Requirement Coverage**（每条需求 → 任务）。
- **每任务字段：** **`Covers:`**（逐字标题）、**`read_first`**、**Acceptance criteria**、**Verification**、**Dependencies**、**Files likely touched**、**Estimated scope**、**TDD** + 勾选子步骤（命令 + 期望结果）。
- **流程：** 规划阶段只读；正文只允许叙述与命令—不写实现源码；交给 **`sdd-build`**。

### `sdd-build`

- **作用：** 编排按计划逐个执行任务（对应 superpowers 的「按计划顺序执行」范式）。
- **上下文：** `docs/plans/...`、当前任务包、必要时 feature 与 `PROJECT.md`。
- **产物：** 已完成任务的实现与验证证据。
- **流程：** 校验输入与文件归属；行为变更走 TDD；上下文收窄；安全时把独立切片交给 **`sdd-subagent-build`**。

### `sdd-subagent-build`

- **作用：** 在**全新** subagent 中执行**单个**独立任务（superpowers 的「每任务独立工人」范式）。
- **上下文：** 单个任务包：**`Covers`**、验收、验证、**`read_first`**、**Files likely touched** 等。
- **产物：** 改动文件、命令输出、证据或阻塞—然后把控制权交回 **`sdd-build`**。
- **流程：** 确认归属 → 窄范围实现 → 验证 → 汇报—不在工人里改计划或做正式 review。

### `sdd-review`

- **作用：** **五轴合并就绪评审** — **正确性**、**可读性与简洁性**、**架构**、**安全**、**性能**（详见 **`skills/sdd-review/SKILL.md`** → **The Five-Axis Review**）。

- **分级（Step 4 — Categorize Findings）：** 每条 finding 带 **emoji** 前缀，区分是否阻塞合并：

  | Emoji | 级别 | 常见期望 |
  |-------|------|----------|
  | 🔴 | **Critical（关键）** | 阻塞合并，必须修（安全、丢数据、功能错误等）。 |
  | 🟠 | **Important（重要）** | 合并前应处理（或显式延期并说明理由）。 |
  | 🟡 | **Suggestion（建议）** | 可选改进。 |
  | 🔵 | **Info（信息）** | 仅背景说明，不要求改动。 |

  全文定义见 **`skills/sdd-review/SKILL.md`** → **Review Process** → **Step 4**。输出模版（含 **What Went Well**）：**`agents/code-reviewer.md`**。

- **范围：** 面向 **本次待合并的改动** — 通常是 **diff / 变更文件**、**相关测试**、作者的 **验证说明**，并对照 **规格或任务** 意图。**默认不**把 **`docs/features/`**、**`docs/plans/`** 整份当审计对象；除非你明确要求 **规格对齐**。**`skills/sdd-review/references/`** 用于 **按需加深**，不是强制整库扫描。

- **上下文：** 规格或任务、测试与实现（见 SKILL **Review Process**）。

- **产物：** 分级反馈与结论；可选 **`agents/code-reviewer.md`** 中的 **Review Summary** 模板。深度清单：**`skills/sdd-review/references/`**。

- **流程：** 只读评审；若支持 subagent，可在新上下文派发 **`agents/code-reviewer.md`** + SKILL。

### `sdd-simplify`

- **作用：** 在用户约定的简化范围内降复杂度，不改变既定行为。
- **范围：** 仅做 **行为不变** 的重构（**`skills/sdd-simplify/SKILL.md`**）。在事先约定的 **命名范围**（路径、模块、「本次
  PR」）内动手；优先 **最近改动 / 与任务相关** 的代码，避免范围外的 **顺手重构**。**`docs/features/`**、**`docs/plans/`**
  可作上下文，**默认不**作为改写对象，除非你点名。
- **上下文：** 用户点名的范围（路径/模块/「本次改动」）+ 范围内代码；**`docs/features/`**、**`docs/plans/`** 仅作可选上下文。
- **产物：** 更简实现 + 行为未变证据。
- **流程：** 只动约定范围内；边界不清或改动大则回到 **`sdd-review`**。

### `sdd-release`

- **作用：** 把已批准变更打包成交付物。
- **上下文：** 已 review（及可选 simplify）结果、本会话验证证据。
- **产物：** 清单 + 对外摘要（版本、排除项、交接选项）。
- **流程：** 冻结候选、release notes、回滚提示—无本会话证据不宣称完成。

## 如何测试

这个仓库主要是文档和技能文件，所以最容易的测试方式，是拿一个新项目的例子把整条技能链走一遍。

可以用这段作为新项目演练的开场提示词：

```text
我想做一个任务管理功能
请写规格
拆成可执行任务
按计划实现
请评审这次改动
现在能上线吗
```

## 来源映射

| 技能                   | 参考来源                                                                                                         |
|----------------------|--------------------------------------------------------------------------------------------------------------|
| `sdd-init`           | [sivaprasadreddy/sdd-skills](https://github.com/sivaprasadreddy/sdd-skills)                                  |
| `sdd-brainstorm`     | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-feature`        | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| `sdd-plan`           | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-build`          | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-subagent-build` | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-review`         | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)                                        |
| `sdd-simplify`       | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)                                                |
| `sdd-release`        | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |

## 参考文章

- [OpenSpec getting started](https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md) —
  规范作为事实源、提案 / 应用 / 归档流程、上下文卫生。
- [GSD README](https://github.com/gsd-build/get-shit-done/blob/main/README.md) — 工作流控制、并行化和 subagent 注入。
- [GSD plan phase](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/plan-phase.md) — 带
  `read_first` 和 `acceptance_criteria` 的硬任务模板。
- [GSD verify work](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/verify-work.md) — 增量验证和
  UAT 风格反馈回路。
- [superpowers](https://github.com/obra/superpowers) — review before merge、verification before completion，以及并行分发规则。
- [sdd-skills](https://github.com/sivaprasadreddy/sdd-skills) — `sdd-init` 的初始化模式和项目上下文采集。

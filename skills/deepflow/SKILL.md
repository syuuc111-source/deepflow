---
name: deepflow
description: DeepFlow（深度工作流）的 Codex 适配入口。用户明确要求使用 DeepFlow，或按其工作流开展需求探索、编写规格、依赖排序执行任务及依据验收标准验证结果时使用。基于 saidwafiq/deepflow；中文说明，保留上游资料。
---

# DeepFlow（深度工作流）

本技能是 `saidwafiq/deepflow` 的本地 Codex 适配入口，不是上游官方 Codex 发行版。上游源码单独安装，来源和固定提交见本文件末尾。解析 `<SOURCE_ROOT>` 为 `$CODEX_HOME/skill-sources/deepflow`；未设置 CODEX_HOME 时使用 `~/.codex/skill-sources/deepflow`，也可使用用户明确指定的已有检出目录。先展开为绝对路径并确认存在；缺失时提示按仓库 README 安装。默认用中文沟通。

## 使用入口

用户可以说：

- `$deepflow 帮我梳理这个功能的需求`
- `$deepflow 根据刚才的讨论编写规格`
- `$deepflow 执行 specs/功能名.md`
- `$deepflow 检查这个功能是否满足验收标准`

先根据用户已给出的目标确定阶段，不重复询问已经回答的问题。只读取当前阶段需要的上游文档，不一次加载全部资料。

| 阶段 | 对应上游文档 | 工作内容 |
|---|---|---|
| 探索需求 | `<SOURCE_ROOT>/src/commands/df/discover.md` | 梳理动机、现状、范围、约束、成功标准及不做的内容 |
| 分析代码库 | `<SOURCE_ROOT>/src/commands/df/map.md` | 基于实际代码记录结构、影响范围和验证入口 |
| 编写规格 | `<SOURCE_ROOT>/src/commands/df/spec.md` | 将需求转为可验证规格及 `## Tasks (curated)` 任务清单 |
| 执行任务 | `<SOURCE_ROOT>/src/commands/df/execute.md` | 按任务依赖顺序执行，记录进度并验证结果 |
| 验收 | `<SOURCE_ROOT>/src/commands/df/verify.md` | 对照规格、测试与实际运行结果检查是否完成 |
| 技能评估 | `<SOURCE_ROOT>/src/commands/df/eval.md` | 用户要求评估时才检查评估工具和所需依赖 |

当前固定版本的 `execute.md` 明确采用主会话串行执行；README 中关于并行编排的描述可能滞后，以当前阶段文档和实际源码为准。

## Codex 工具映射

上游文档中的 Claude 专有名称描述的是工作意图，不代表 Codex 中存在同名工具。

- `Read`、`Glob`、`Grep`：使用实际可用的文件读取、`rg` 和文件枚举。
- `Write`、`Edit`：使用实际可用的文件编辑工具。
- `Bash`：在当前终端执行等价命令。Windows 下改写为正确的 PowerShell 命令，不能直接套用 Bash 变量、重定向或删除命令。
- `AskUserQuestion`：使用当前可用的用户输入工具或普通对话，遵守该工具真实参数，不照抄 Claude 参数。
- `Skill`：读取下方对应的本地 `SKILL.md` 文件，再依照其适用内容工作。
- `TaskCreate`、`TaskUpdate`、`TaskList`：若没有对应工具，在项目内的任务文档记录进度，不声称已调用不存在的工具。
- 子代理和浏览器：仅使用当前确实提供的工具。缺少工具时明确限制，不虚构调用、模型名称或验证结果。

## 配套技能资料

按需读取，均为上游原文：

- 需求缺口：`<SOURCE_ROOT>/src/skills/gap-discovery/SKILL.md`
- 仓库检查：`<SOURCE_ROOT>/src/skills/repo-inspect/SKILL.md`
- 网页读取：`<SOURCE_ROOT>/src/skills/browse-fetch/SKILL.md`
- 浏览器验证：`<SOURCE_ROOT>/src/skills/browse-verify/SKILL.md`
- 验收覆盖：`<SOURCE_ROOT>/src/skills/df-ac-coverage/SKILL.md`
- 决策记录：`<SOURCE_ROOT>/src/skills/df-decisions/SKILL.md`
- 原子提交：`<SOURCE_ROOT>/src/skills/atomic-commits/SKILL.md`

## 兼容性与执行边界

- 本次安装只注册 Codex 技能及资料，没有注册 Claude 的 `/df:*` 斜杠命令、hooks、状态栏或后台编排进程。
- 不运行 `<SOURCE_ROOT>/bin/install.js` 来安装 Codex：它写入的是 `.claude`，并可能修改 Claude 的钩子和权限配置。
- 上游示例中的 `~/.claude/bin`、`~/.claude/templates`、`~/.claude/skills` 分别对应本技能的 `<SOURCE_ROOT>/bin`、`<SOURCE_ROOT>/templates`、`<SOURCE_ROOT>/src/skills`。这只是资料定位；执行脚本前还须检查其内部依赖与硬编码路径。
- 上游自动化脚本未承诺可在 Codex/Windows 直接运行。使用前先阅读对应脚本并核对 Node、Git、Playwright、浏览器等依赖。本次没有安装 npm 依赖或浏览器，也没有验证这些集成。
- 不因安装或调用本技能自动创建提交、合并、推送、删除规格、重置工作区或修改权限；依据用户当前授权及现行系统规则处理上游示例中的这些操作。
- 保存真实的命令和验证结果。未运行、被阻止、失败与通过必须区分；不能把模拟输出当作真实验证。

## 来源

仓库：`https://github.com/saidwafiq/deepflow`

固定提交：`122b53859786d628933b6c08d39d66afa549ef0a`

许可见 `<SOURCE_ROOT>/LICENSE`。中文入口是本地适配说明，上游原文件保持不变。

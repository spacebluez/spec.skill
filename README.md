# mew-spec

`mew-spec` 是一个面向 Claude Code 的版本化 Spec 驱动开发 Skill。它要求在编写实现代码前，先通过协作式需求澄清和逐阶段审批，形成完整的设计与验收依据。

## 工作流

```text
chat.md（记录需求澄清）
  +
spec.md（做什么）
  ↓
plan.md（怎么做）
  ↓
task.md（按什么顺序做）
  ↓
checklist.md（做对了没）
  ↓
开发 → 验收
```

核心约束：

- 每次运行使用独立的 `mew/Tn/` 版本目录。
- `chat.md` 只记录初始提示词和需求澄清阶段，并在生成 `spec.md` 前停止追加。
- Claude 提出选择题时，`chat.md` 完整保留问题和全部选项；用户仅回复编号或字母时，同时记录其对应的完整选项内容。
- `spec.md`、`plan.md`、`task.md` 和 `checklist.md` 必须依次生成并由用户明确批准。
- 四份文档全部获批前，不得编写实现代码。
- 开发按 `task.md` 执行，验收按 `checklist.md` 逐项验证。
- 已有版本目录只读，新迭代不得修改历史版本。

完整规则见 [`SKILL.md`](SKILL.md)。

## 仓库结构

```text
.
├── README.md   # 项目说明、安装与使用方式
└── SKILL.md    # Skill 元数据及完整执行规则
```

## 安装

Claude Code 的用户级 Skill 默认位于 `~/.claude/skills/`。任选一种方式安装。

### 复制安装

```bash
mkdir -p ~/.claude/skills/mew-spec
cp SKILL.md ~/.claude/skills/mew-spec/SKILL.md
```

### 软链接安装

在本仓库根目录执行：

```bash
mkdir -p ~/.claude/skills
ln -s "$(pwd)" ~/.claude/skills/mew-spec
```

如果目标路径已经存在，请先确认其中是否有需要保留的内容，再决定更新或移除；不要直接覆盖。

安装后重新启动 Claude Code 会话，使 Skill 被重新发现。

## 使用

在目标项目的 Claude Code 会话中输入：

```text
/mew-spec 为用户资料导出功能制定规格并实现
```

Skill 会先检查目标项目上下文，然后确定本次唯一可写的版本目录：

```text
mew/
└── T1/
    ├── chat.md
    ├── spec.md
    ├── plan.md
    ├── task.md
    └── checklist.md
```

如果 `T1` 已有内容，下次运行会使用 `T2`，依此递增。版本号按数字比较，因此 `T10` 晚于 `T9`。

## 各文档职责

| 文档 | 职责 |
| --- | --- |
| `chat.md` | 按时间顺序记录初始提示词和需求澄清；完整保留选择题上下文，不需要单独审批 |
| `spec.md` | 描述背景、目标、需求、边界和可观测验收标准 |
| `plan.md` | 定义架构、接口、数据结构、模块交互和技术决策 |
| `task.md` | 给出具体文件清单、有序任务、依赖和验证方式 |
| `checklist.md` | 覆盖需求验收、集成检查、编译测试和端到端场景 |

## 适用场景

在开始以下工作前使用：

- 新功能或新模块；
- 行为变化或架构调整；
- 配置改动和单文件功能；
- 需要留下版本化需求、设计和验收记录的开发任务。

即使改动很小，也不跳过文档和审批阶段；文档可以简短，但必须完整且可验证。

# Resume Agent — 面试复盘积累系统

> 贴面经 → 结构化存储 → 岗位匹配 → 弱点攻克。为求职者打造的面试备战闭环。

## What it does

把每次面试转化为**可检索、可追踪、可改进**的结构化资产。不生成完整简历——专注积累与备战。

| 模式 | 触发 | 产出 |
|------|------|------|
| **面试复盘** | 贴面经/复盘文本 | SQLite 存储 + 三段反馈 + 错题本检测 |
| **岗位分析** | 贴 JD | 四维度经历匹配 + 面试备战警告 + 简历Bullet(含岗位映射) + 补齐方案 + 项目复盘 + 思维训练 + 面试准备 |
| **批量处理** | "处理待处理岗位" | 对所有待投递岗位跑通岗位分析全流程 |
| **错题练习** | "错题本" | 弱点聚类 + v1/v2/v3 答案版本管理 |

## Installation

### Claude Code (CLI)

```bash
# 1. Add the marketplace
claude plugin marketplace add YaaLO-o/resume-agent

# 2. Install the plugin
claude plugin install resume-agent@resume-agent
```

### Claude Cowork

1. Open **Customize** (bottom-left)
2. Go to **Browse plugins** → **Personal** → **+**
3. Select **Add marketplace from GitHub**
4. Enter: `YaaLO-o/resume-agent`

## Quick Start

```
# 面试复盘：贴你的面经
今天面了字节的产品岗，面试官问了...

# 岗位分析：贴 JD
岗位职责：负责xxx...

# 批量处理
处理待处理岗位

# 错题练习
错题本
```

## How It Works

### Skills = 面试知识框架

`resume-agent` skill 加载候选人身份、写作铁律、ATS 规则、匹配算法等面试备战知识。Claude 在对话中自动应用。

### Commands = 四模式流水线

| Command | 说明 |
|---------|------|
| `/resume-agent:interview-review` | 面试复盘：解析面经 → 结构化存储 → 生成三段反馈 |
| `/resume-agent:job-analysis` | 岗位分析：JD解析 → 经历匹配 → 简历Bullet → 补齐方案 → 项目复盘 → 思维训练 → 面试准备 |
| `/resume-agent:batch-process` | 批量处理：读取投递追踪表 → 对每条待处理岗跑通全流程 |
| `/resume-agent:mistake-practice` | 错题练习：弱点聚类 → 答案版本管理 |

### 反馈回路

```
面试复盘 → SQLite + markdown → 岗位分析检索历史 → 面试备战警告 → 面试 → 复盘 → 循环 → 错题练习攻克弱点
```

每次复盘都在喂养下一次面试准备。

## Requirements

- Claude Code 或 Claude Cowork

## Optional: Excel 数据层增强

本 skill 为纯 Markdown 包，核心功能由 Claude 直接完成。如需更强的数据持久化和批量处理能力，可搭配 Python 数据层使用：

| 能力 | 纯 skill（本包） | + Excel 数据层 |
|------|-----------------|---------------|
| 面试复盘 | 聊天输出反馈 | SQLite 存储 + markdown 镜像 |
| 岗位分析 | 聊天输出 Bullet | 写入「简历Bullet」sheet，按公司+岗位归档 |
| 批量处理 | 逐条手动贴 JD | 读取投递追踪表，自动批量跑通全流程 |
| 错题练习 | 聊天查看 | SQLite 统计弱点频率 + 答案版本管理 |
| 追踪回填 | 手动记录 | 自动回填投递追踪表各 sheet |

数据层为独立项目，需要 Python 3.10+ 和 openpyxl。

### 空白模板

本包提供 3 个空白 Excel 模板，下载即用：

| 模板 | 用途 | Sheet |
|------|------|-------|
| `templates/投递追踪模板.xlsx` | 投递管理主表 | 投递追踪、简历Bullet、补齐方案、项目复盘、思维训练、面试准备、学习计划 |
| `templates/复盘导入模板.xlsx` | 面试复盘批量导入 | 面试复盘导入（序号/公司/岗位/日期/轮次/问题/类型/质量/卡壳/标签/改进） |
| `templates/复盘追踪模板.xlsx` | 岗位复盘追踪 | 复盘追踪（匹配度评分、弱点标签、经历映射策略） |

**使用方式**：下载模板 → 填入你的数据 → 配合数据层脚本自动处理，或直接手动使用。

## Customization

### 修改候选人信息

编辑 `skills/resume-agent/SKILL.md` 中的「候选人信息」部分。

### 修改经历库

在项目的 `database/` 目录下编辑对应的 markdown 文件：
- `internships.md` — 实习经历
- `projects.md` — 项目经历
- `competitions.md` — 竞赛获奖
- `leadership.md` — 社会实践

### 修改匹配规则

编辑 `rules/matching-rules.md` 中的四维度打分权重。

## Architecture

```
resume-agent/
├── .claude-plugin/
│   ├── marketplace.json     # Marketplace manifest
│   └── plugin.json          # Plugin manifest
├── skills/
│   └── resume-agent/
│       └── SKILL.md         # 核心 skill（知识框架 + 模式路由）
├── commands/
│   ├── interview-review.md  # 模式一：面试复盘
│   ├── job-analysis.md      # 模式二：岗位分析
│   ├── batch-process.md     # 模式三：批量处理
│   └── mistake-practice.md  # 模式四：错题练习
├── templates/               # 空白 Excel 模板
│   ├── 投递追踪模板.xlsx     # 7 个 sheet 的投递管理主表
│   ├── 复盘导入模板.xlsx     # 问答级面试复盘导入
│   └── 复盘追踪模板.xlsx     # 岗位复盘追踪
├── README.md                # 本文件
├── CLAUDE.md                # Agent guidance
└── LICENSE                  # MIT
```

## Author

Your Name

## License

MIT

## License

MIT

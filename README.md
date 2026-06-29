# Resume Agent — 面试复盘积累系统

> 贴面经 → 自动解析 → 岗位匹配 → 弱点攻克。你只管贴，剩下的交给 AI。

## 它能干什么

把每次面试变成**可检索、可追踪、可改进**的结构化资产。不生成完整简历——专注积累与备战。

| 模式 | 你做什么 | AI 自动做什么 |
|------|---------|-------------|
| **面试复盘** | 贴面经/复盘文本 | 解析问答 → 评估质量 → 生成三段反馈 → 检测错题候选 |
| **岗位分析** | 贴 JD | 四维度匹配经历 → 写简历Bullet → 生成补齐方案/项目复盘/思维训练/面试准备 |
| **批量处理** | 说"处理待处理岗位" | 自动遍历所有待投递岗位，跑通全流程 |
| **错题练习** | 说"错题本" | 聚类弱点 → 生成 v1/v2/v3 三个版本答案 |

## 安装

### Claude Code（终端）

```bash
claude plugin marketplace add YaaLO-o/resume-agent
claude plugin install resume-agent@resume-agent
```

### Claude Cowork（桌面端）

1. 打开 **Customize**（左下角）
2. 进入 **Browse plugins** → **Personal** → **+**
3. 选 **Add marketplace from GitHub**
4. 输入：`YaaLO-o/resume-agent`

## 快速上手

安装完成后，直接在对话中使用：

```
# 面试复盘：贴你的面经
今天面了字节的产品岗，面试官问了xxx，我回答了xxx...

# 岗位分析：贴 JD
岗位职责：负责xxx...任职要求：xxx...

# 批量处理：一键处理所有待投递
处理待处理岗位

# 错题练习
错题本
```

## 工作原理

```
你贴面经 → Claude 自动解析问答对、评估回答质量、分类问题类型
         → 存入结构化数据 + 生成三段反馈
         → 下次分析岗位时，自动检索历史面试数据
         → 交叉匹配弱点 → 生成面试备战警告
         → 面试 → 复盘 → 循环 → 错题攻克
```

每次复盘都在喂养下一次面试准备。

## 四个命令

| 命令 | 说明 |
|------|------|
| `/resume-agent:interview-review` | 面试复盘：解析面经 → 结构化存储 → 三段反馈 |
| `/resume-agent:job-analysis` | 岗位分析：JD解析 → 经历匹配 → 简历Bullet → 补齐方案 → 项目复盘 → 思维训练 → 面试准备 |
| `/resume-agent:batch-process` | 批量处理：读取投递追踪表 → 对每条待处理岗跑通全流程 |
| `/resume-agent:mistake-practice` | 错题练习：弱点聚类 → 答案版本管理 |

## Excel 模板（可选增强）

本包自带 3 个空白 Excel 模板，**下载即用**：

| 模板 | 用途 | 包含 Sheet |
|------|------|-----------|
| `templates/投递追踪模板.xlsx` | 投递管理主表 | 投递追踪、简历Bullet、补齐方案、项目复盘、思维训练、面试准备、学习计划 |
| `templates/复盘导入模板.xlsx` | 面试复盘批量导入 | 问答级导入（公司/岗位/日期/轮次/问题/类型/质量/卡壳/标签） |
| `templates/复盘追踪模板.xlsx` | 岗位复盘追踪 | 匹配度评分、弱点标签、经历映射策略 |

### 使用方式

**你只需要做一件事：在「投递追踪」sheet 填写公司和岗位名称。**

其他所有列（岗位类型、匹配方向、主力经历、简历Bullet、补齐方案、项目复盘……）全部由 AI 自动解析填充。

```
第 1 步：下载「投递追踪模板.xlsx」
第 2 步：在「投递追踪」sheet 填写：公司名 + 岗位名 + JD原文
第 3 步：对 Claude 说"处理待处理岗位"
第 4 步：AI 自动跑通全流程，回填所有 sheet
```

| 你需要填的 | AI 自动完成的 |
|-----------|-------------|
| 公司名 | 岗位类型判定 |
| 岗位名 | 匹配方向分析 |
| JD 原文 | 主力经历推荐 |
| — | 简历 Bullet（含公司+岗位标注） |
| — | 能力缺口 + 补齐方案 |
| — | 项目复盘（灵魂三问+三种时长版本） |
| — | 产品思维训练 |
| — | 面试准备清单 |
| — | 学习计划 |

## 个性化配置

### 填写你的信息

编辑 `skills/resume-agent/SKILL.md` 中的「候选人信息」部分，把占位符替换为你的真实信息：

```markdown
## 候选人信息

- {你的姓名}，{你的学校} {你的专业} {你的届别}
- 求职方向：{方向1}、{方向2}
- 核心经历：{经历1} + {经历2} + {经历3}
```

### 添加你的经历

在 `database/` 目录下编辑对应的 markdown 文件：
- `internships.md` — 实习经历
- `projects.md` — 项目经历
- `competitions.md` — 竞赛获奖
- `leadership.md` — 社会实践

## 项目结构

```
resume-agent/
├── .claude-plugin/           # 插件清单
├── skills/resume-agent/
│   └── SKILL.md              # 核心 skill（模式路由 + 约束规则）
├── commands/
│   ├── interview-review.md   # 模式一：面试复盘
│   ├── job-analysis.md       # 模式二：岗位分析
│   ├── batch-process.md      # 模式三：批量处理
│   └── mistake-practice.md   # 模式四：错题练习
├── templates/                # 空白 Excel 模板
│   ├── 投递追踪模板.xlsx
│   ├── 复盘导入模板.xlsx
│   └── 复盘追踪模板.xlsx
├── README.md
├── CLAUDE.md
└── LICENSE                   # MIT
```

## 环境要求

- Claude Code 或 Claude Cowork
- 无其他依赖（纯 skill，开箱即用）

## License

MIT

# 面试复盘

用户发送面试复盘/面经/面试反馈文本时触发。仅接受文字输入（Word/TXT/Markdown 文件或直接粘贴文本）。

## Step 1：接收并解析

1. 接收文字内容（粘贴文本或上传 .txt/.md/.docx 文件）
2. 提取元信息（公司、岗位、日期、轮次）

> **不支持音频文件**。如用户提供录音，请提示："请先将录音转写为文字，再贴给我。"

## Step 2：结构化解析

让 Claude 将原始文本解析为结构化 JSON：

```
你是面试复盘分析器。候选人信息见系统设定。

解析以下面试复盘文本，抽取所有问答对。不要概括或跳过。

对每个问题：
- question_type: 简历深挖/产品/业务场景/估算/算法/系统设计/行为/其他
- answer_rating: 优(有数字+方法+结果)/中(缺一项)/差(空洞/答非所问/逻辑混乱)
- good_flag: true 如果答得好且面试官表示兴趣
- stuck_flag: true 如果候选人挣扎、答得差、或被追问质疑
- 估算题需提取 estimation_model: {approach, parameters, calculation_steps, result}
- tags: 从 [缺数字, 缺产品决策, 技术实现太重, STAR不完整, 缺业务结果, 缺方法论, 逻辑跳跃, 答非所问] 中选相关标签
- confidence_score: 0-1 的评分置信度

只返回 JSON：
{"meta": {...}, "questions": [...], "weakness_tags": [...], "new_facts": [...]}
```

## Step 3：问题分类

对每个问答对进行分类：
- 8 种问题类型映射
- 回答质量评估 (优/中/差)
- 估算题参数检测
- 置信度计算

## Step 4：结构化存储

将解析结果保存为结构化数据：

**有 SQLite 时**：写入 `database/interview.db`，用 `with conn:` 事务包裹。
- interviews 表：元信息
- interview_questions 表：问答明细
- processing_logs 表：处理日志

**无 SQLite 时**：以 Markdown 格式存储到 `database/interview-log.md`，追加写入。

## Step 5：生成三段反馈

**段一：总体印象 (1-2句)**
概括面试侧重点和整体表现，引用实际问到的题目类型。

**段二：具体改进建议 (3-5条，按优先级)**
每条含 Priority/Title/Detail/Actionable。

**段三：后续行动 (2-3项)**

若信息不足（多个问答对 confidence_score < 0.5），附加「需要补充」清单。

## Step 6：检查错题本候选

识别需要加入错题本的问答：
- answer_rating='差' 或 stuck_flag=True 的问答对
- 若同类问题已有 ≥2 次记录 → 自动加入 mistake_book
- 更新 frequency 计数

**有 SQLite 时**：写入 mistake_book 表。
**无 SQLite 时**：追加到 `database/mistake-book.md`。

## Step 7：更新 markdown 记录

**7a. 追加到 interview-log.md**：

```markdown
## [公司] 岗位 | 日期 | 轮次

**岗位类型**: {岗位类型}
**涉及经历**: {经历1}, {经历2}
**弱点标签**: {tag1}, {tag2}

### 问答记录
| # | 问题 | 类型 | 回答质量 | 卡壳 | 改进方向 |
|---|------|------|----------|------|----------|
| 1 | xxx  | 简历深挖 | 中 | 否 | 补充数字 |
```

**7b. 若发现新事实**（new_facts 非空）：追加到对应 `database/*.md` 经历文件（不覆盖已有内容）。

## Step 8：展示结果

在对话中展示三段反馈。

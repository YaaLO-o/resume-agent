# 错题练习

用户说"错题本""练习模式""mistake""弱点练习"时触发。

## Step 1：查询弱点数据

**有 SQLite 时**，执行以下查询：

```sql
-- Top 弱点类型
SELECT question_type, COUNT(*) as cnt,
       SUM(CASE WHEN answer_rating='差' THEN 1 ELSE 0 END) as bad_cnt,
       SUM(CASE WHEN stuck_flag=1 THEN 1 ELSE 0 END) as stuck_cnt
FROM interview_questions
GROUP BY question_type
ORDER BY cnt DESC;

-- 高频卡壳问题
SELECT iq.question_text, iq.question_type, iq.answer_rating,
       i.company, i.date, iq.stuck_flag
FROM interview_questions iq
JOIN interviews i ON iq.interview_id = i.id
WHERE iq.answer_rating='差' OR iq.stuck_flag=1
ORDER BY i.date DESC;

-- 错题本
SELECT * FROM mistake_book ORDER BY frequency DESC;
```

> **SQL 安全**：上述查询为静态模板，不拼接用户输入。如需动态查询，使用参数化查询（`?` 占位符）。

**无 SQLite 时**，从以下 markdown 文件读取：
- `database/interview-log.md` — 找「回答质量: 差」和「卡壳: 是」的记录
- `database/mistake-book.md` — 读取错题本（如存在）

> **注意**：无 SQLite 时，Markdown 文件的查询精度有限。若数据量大（>20 条面试记录），建议配置 SQLite 以获得准确的弱点统计。

## Step 2：弱点归类展示

```
你的 Top 3 弱点领域：
1. 估算题 (8次, 75%卡壳率) — 急需系统学习费米问题
2. 产品sense (5次, 60%评为"中") — 缺少产品决策框架
3. 行为问题 (3次, 33%评为"差") — STAR不完整
```

## Step 3：生成答案版本

对高频卡壳问题，生成三个版本：
- **v1 (初期)**：面试当时的回答
- **v2 (优化版)**：AI 建议改进版
- **v3 (进阶版)**：深度优化版（结合追问角度）

**有 SQLite 时**：存入 `interview_questions.versions_json`。
**无 SQLite 时**：在对话中直接展示三个版本，并追加到 `database/mistake-book.md`（含版本记录）。

## Step 4：生成练习建议

2-3 条可执行的练习方案。

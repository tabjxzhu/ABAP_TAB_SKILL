# SAP TAB Article Patterns

Use this reference when turning knowledge cards into articles or extracting Skill assets from articles.

## Non-Negotiable Quality Bar

An article is not ready if it only gives a generic checklist. Each article must contain:

1. One narrow ABAP/SAP problem.
2. A desensitized but concrete scenario.
3. A minimal reproduction or equivalent test setup.
4. One risky/incorrect code example.
5. One improved code example.
6. A troubleshooting evidence chain.
7. A verification method.
8. A reusable Skill rule.

If the source material cannot support these, ask for more source notes instead of expanding with generic AI text.

## Article Structure

1. Title
2. Summary
3. Scenario
4. Desensitized story
5. Four-panel comic script
6. Minimal reproduction
7. Symptom
8. Risky or incorrect code
9. Investigation evidence chain
10. Improved code or configuration
11. Verification method
12. Notes and risks
13. AI asset card

## Style

- Write like an experienced ABAP developer explaining a real case to another developer.
- Keep the judgment path visible.
- Prefer concrete SAP/ABAP terms where verified: ST05, SAT, SQLM, ST22, SM37, SM12, SM13, SLG1, SU53, ATC, SCI, Open SQL, BAPI RETURN, update task, enqueue lock, secondary key.
- Every technical claim should be backed by one of: code, tool output, transaction path, runtime symptom, SAP documentation, or source knowledge card.
- Avoid generic textbook explanations unless they support the case.
- Mark uncertain details as `[需要确认]`.
- Include one desensitized mini story and convert it into a four-panel comic script by default.
- For each comic panel, define a scene, a short primary caption, and a short secondary caption.

## Title Patterns

- `ST22 看到 <dump>：先看这 3 个字段再改代码`
- `<ABAP construct> 慢，不一定是数据库慢：用 <tool> 先切开`
- `<technique> 不是不能用，关键是 <specific pitfall>`
- `这段 <Open SQL/BAPI/RAP/IDoc> 代码看着没错，为什么生产会出问题`
- `AI 写 <ABAP object>：这类隐藏风险必须人工 review`

## Code Example Rules

- Use concrete but desensitized names: `z_demo_order`, `zcl_demo_service`, `Z_DEMO_BAPI_CHANGE`.
- Show the risky code first, then the improved code.
- The improved code must change something meaningful: key definition, WHERE condition, commit boundary, exception handling, logging, authorization, lock handling, or verification.
- Do not use code that is only syntactically illustrative and has no diagnostic value.

## AI Asset Card

Every article should end with a reusable extraction:

- 可复用规则：
- 代码模板：
- 排查 checklist：
- Prompt 模板：

Do not include “后续选题” in the AI asset card.

## Desensitization

Remove or generalize:

- Customer names.
- Project names.
- Hostnames, IPs, system IDs, clients.
- User IDs and personal information.
- Real document numbers and business data.
- Screenshots with sensitive fields.

## Distribution

For each article, prepare:

- WeChat official account title.
- WeChat summary.
- Zhihu/CSDN/Juejin summary.
- Short social post.
- Keywords and tags.

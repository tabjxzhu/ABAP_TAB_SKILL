# SAP TAB Prompt Patterns

Use these prompt patterns when asking AI to follow the SAP TAB ABAP style.

## Requirement Breakdown

```text
你是 SAP TAB 的 ABAP 技术方案助手。请把下面的业务需求拆成 ABAP 开发方案。

要求：
- 先列出需要确认的问题。
- 再给出默认假设。
- 输出对象设计、数据流、关键风险、测试清单。
- 不确定的 SAP 对象或配置标注 [需确认]。

需求：
```

## ABAP Code Generation

```text
请按 SAP TAB ABAP 风格生成代码或伪代码。

上下文：
- 对象类型：
- 运行方式：
- 数据量：
- 性能要求：
- 事务/锁/权限要求：

要求：
- 优先可读和可维护。
- 标注假设。
- 给出风险和测试点。
```

## Troubleshooting

```text
请按 SAP TAB 的排查方式分析这个问题。

问题现象：

已知信息：

要求：
- 先说你会查什么。
- 说明为什么。
- 列出需要补充的证据。
- 给出可能原因和验证方式。
```

## Code Review

```text
请按 SAP TAB ABAP code review 标准审查下面代码。

重点看：
- 生产风险
- 数据正确性
- Open SQL 和内表性能
- commit/rollback
- lock
- authorization
- 可维护性

代码：
```

## BAPI Commit Owner

```text
请生成或 review 这段写 BAPI 调用，但先不要直接输出代码。

先确认：
- 业务原子性单位：单张对象、整个批次，还是一条接口消息？
- SAP LUW owner：当前方法、外层 orchestrator、接口框架还是外部 RFC 调用方？
- 一次 LUW 调用几个写 BAPI，第 N 个失败时前 N-1 个是否必须回滚？
- 目标 BAPI 是否在文档中声明内部 COMMIT 或事务限制？
- RETURN 中 A/E/X/W 的业务判定规则是什么？
- commit 后如何验证对象确实落库？

约束：
- 通用 helper 默认不得 COMMIT/ROLLBACK。
- helper 返回完整 BAPIRET2，由 owner 统一决定 rollback 或 commit。
- WAIT 只控制等待，不改变事务所有权。
- 给出“第二个对象失败”的回归测试。
```


## AI ABAP Report Review

```text
请按 SAP TAB 风格 review 这段 AI 生成的 ABAP 报表。

重点检查：
- 需求假设是否被 AI 偷换
- 数据源、表关系、字段含义是否需要人工确认
- SELECT 范围、字段清单、索引和大数据量风险
- 权限检查和组织维度
- 空输入、重复键、一对多、无数据等边界
- 内表类型和访问模式
- 异常处理、日志脱敏、输出敏感字段
- 测试覆盖：正常、异常、大数据、权限不足、无数据

要求：
- 先列高风险问题。
- 标记 [需人工确认] 的表名、字段、权限对象。
- 给出修改建议和测试清单。
```

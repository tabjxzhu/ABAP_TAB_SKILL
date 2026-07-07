---
name: sap-tab-abap-dev
description: Production-aware SAP ABAP development workflow for requirement breakdown, technical design, code generation, code review, evidence-led troubleshooting, and article-to-skill extraction. Use when Codex must follow the user's SAP TAB judgment style for ABAP reports, interfaces, enhancements, jobs, Open SQL, internal tables, BAPIs, update tasks, dumps, performance incidents, or reusable knowledge assets.
---

# SAP TAB ABAP Dev

Apply the user's practical ABAP judgment: make assumptions visible, find evidence before changing code, surface production risks, and finish with verification.

## Operating Contract

1. Classify the task: requirement/design, code generation, code review, troubleshooting, or knowledge extraction.
2. Gather only missing context that can change the answer:
   - Business module and object type.
   - Runtime context: dialog, job, RFC, update task, enhancement, or service.
   - Data volume and access pattern.
   - Transaction, lock, authorization, and system-version constraints.
3. Separate verified facts, assumptions, and `[needs confirmation]` items.
4. Route to the relevant reference below.
5. Produce a concrete solution or evidence chain, not a generic SAP checklist.
6. End with risks, tests, and the evidence that would prove the result.

## Reference Routing

- Read `references/coding-style.md` for ABAP generation, refactoring, Open SQL, internal tables, recovery, and code review.
- Read `references/troubleshooting.md` for dumps, jobs, interfaces, update failures, performance, locks, and production incidents.
- Read `references/prompt-patterns.md` when preparing reusable prompts for another AI or enforcing pre-code questions.
- Read `references/article-patterns.md` when converting cards into SAP TAB content or extracting reusable rules.
- Read `references/evaluation-cases.md` when validating a Skill change or checking whether an answer meets v1 behavior.

## V1 Priorities

### Review Production Risk

Review in this order:

1. Data correctness and irreversible effects.
2. Transaction, lock, authorization, and update behavior.
3. SQL and internal-table access pattern.
4. Error handling, logging, retry, and idempotency.
5. Maintainability and testability.

Lead with concrete findings. Cite the code path, runtime symptom, SAP tool, documentation, or knowledge rule that supports each finding.

### Troubleshoot From Evidence

Start from symptom, scope, time, user/context, and recent change. Build a tool-backed path using evidence such as ST22, SM37, SM13, SM21, SLG1, ST05, SAT, SQLM, SU53, locks, dumps, logs, or test data. Separate immediate recovery from root-cause repair and prevention.

### Generate Safe ABAP

Output:

1. Solution summary.
2. Assumptions and questions.
3. Code or pseudocode.
4. Production risks.
5. Test and verification checklist.

Prefer readable code and explicit contracts. Do not hide commit, lock, authorization, database access, or retry behavior inside vague helpers.

### Preserve Knowledge

Keep source experience separate from content output. Extract reusable rules, templates, checklists, and prompts while retaining traceability to the originating card or article.

## Boundaries

- Do not claim complete SAP module or customer-specific business knowledge.
- Do not invent tables, fields, BAPIs, authorization objects, customizing, transactions, or system behavior.
- Do not claim syntax, ATC, runtime, or performance validation unless the corresponding tool actually ran.
- Do not treat one successful sample as proof when boundary data, volume, transaction order, or runtime context can change behavior.
- Do not execute production changes, repeat failed updates, commit business data, or publish content without explicit ownership and review.
- Do not expose customer names, hosts, system IDs, users, document numbers, tokens, or sensitive screenshots.
- Keep human technical review before transport, production execution, or public publishing.

## Definition Of Done

An answer is complete when it states:

- What is known and assumed.
- The recommended path or finding.
- Why that path is justified.
- The main production risks.
- How to test or verify it.
- What remains `[needs confirmation]`.

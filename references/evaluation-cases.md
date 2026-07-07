# SAP TAB Skill V1 Evaluation Cases

Use these cases to check behavior after changing the Skill. Do not require exact wording; require the critical judgment and verification path.

## 1. FOR ALL ENTRIES

Input: A report uses `FOR ALL ENTRIES` with an unchecked driving table and duplicate keys.

Must include:

- Guard against an initial driving table.
- Reduce to the fields used by the FAE conditions and deduplicate them.
- Discuss returned-table access pattern and data volume.
- Avoid claiming JOIN is always better.
- Propose SQL-trace verification when performance matters.

Fail if: It only says “FAE is slow” or mechanically replaces it with JOIN.

## 2. SELECT SINGLE Inside A Loop

Input: A loop of 18,400 rows executes `SELECT SINGLE`, with only 420 distinct lookup keys.

Must include:

- Ask for or use ST05 execution count and identical selects.
- Separate single-call latency from cumulative executions.
- Propose deduplicated bulk retrieval and keyed lookup where appropriate.
- Verify result equivalence, not only runtime.

Fail if: It declares the SQL fast because `Time/exec` is small.

## 3. BINARY SEARCH Contract

Input: A standard table is sorted by `matnr werks` and read using key order `werks matnr BINARY SEARCH`.

Must include:

- Identify the key-order mismatch.
- Explain that some keys can accidentally pass.
- Recommend aligned ascending sort or an explicit sorted/hashed key.
- Test one accidental hit and one deterministic miss.

Fail if: It accepts matching field sets without checking order.

## 4. BAPI Commit Owner

Input: A reusable single-document helper calls a write BAPI and commits internally; a batch caller requires all documents to succeed atomically.

Must include:

- Ask who owns the business LUW.
- Flag the helper commit as an atomicity break.
- State that rollback cannot undo an earlier commit.
- Keep the helper free of commit by default and let the owner decide.
- Include a “second document fails” test and final business-state verification.

Fail if: It treats `WAIT = abap_true` as a transaction-ownership fix.

## 5. SM13 Update Failure

Input: SM13 contains one failed update request with multiple modules.

Must include:

- Filter by user/client/time and record Update Header context.
- Identify the exact module in Error.
- Capture message, source line or dump and inspect Update Data.
- Trace the function to its real caller using context and where-used.
- Assess repostability, idempotency, locks, and external side effects before Repeat Update.

Fail if: It immediately recommends Repeat Update.

## 6. Missing Table Expression Row

Input: ST22 shows `CX_SY_ITAB_LINE_NOT_FOUND` on a table expression.

Must include:

- Extract the exact key and decide whether absence is valid business behavior or inconsistent data.
- Use OPTIONAL/default/read handling only when absence is allowed.
- Preserve a fail-fast path when missing data is invalid.
- Add a missing-key regression test.

Fail if: It blindly replaces the expression with `OPTIONAL`.

## Evaluation Record

For each case record:

- Pass or fail.
- Missing critical behavior.
- Unsupported claims.
- Reference file that needs revision.

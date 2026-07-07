# SAP TAB ABAP Coding Style

This file captures the first-version SAP TAB ABAP preferences. Expand it with real examples from knowledge cards.

## General Principles

- Prefer readable, boring ABAP over clever ABAP.
- Make data flow obvious: input, processing, output.
- Keep business rules close to the code path where they are applied.
- Name variables for business meaning, not only technical type.
- Avoid hiding risky database, commit, lock, or authorization behavior inside vague helper methods.
- Add comments only when they explain business intent, non-obvious constraints, or production risks.

## Structure

- Split long reports into clear sections: selection screen, validation, data retrieval, processing, output.
- For larger logic, prefer local classes or small FORM routines depending on the codebase style.
- Keep database access and business calculation separable where practical.
- Avoid deep nesting; return early or split logic when readability improves.

## Open SQL

- Confirm expected data volume before choosing a retrieval strategy.
- Avoid `SELECT *` unless the table is small, stable, and the full row is genuinely needed.
- Always think about indexes, filter conditions, and sort requirements.
- Treat `FOR ALL ENTRIES` carefully:
  - Check the driving internal table is not initial.
  - Remove duplicates when appropriate.
  - Select only needed fields.
  - Confirm behavior is acceptable for large driving tables.
  - Build a compact driving table with only the fields used in the FAE conditions.
  - Decide how the returned result will be read: sorted standard table, sorted table, hashed table, or sequential processing.
  - Do not say "replace FAE with JOIN" by default; compare data volume, indexes, one-to-many behavior, system version, and readability.
- Do not rely on implicit database order. Use explicit `ORDER BY` when order matters.
- When SQL appears inside a loop, do not judge it only by one execution's response time. Compare loop rows, distinct lookup keys, and ST05 `Executions`.
- Use ST05 summarized SQL and identical-select views to separate a slow statement from a fast statement executed too many times.
- If repeated lookup keys dominate, prefer a source JOIN where natural; otherwise deduplicate keys, bulk-read deliberately, and store results in a table keyed for the later access pattern.
- Do not promise that `FOR ALL ENTRIES` becomes exactly one database execution; verify the generated access count with ST05.
- Check table buffering before interpreting an empty or small SQL trace. Use Buffer Trace, SAT, or ST12 when accesses may be served outside the database.

## Internal Tables

- Choose table type by access pattern:
  - `STANDARD TABLE` for simple append and sequential processing.
  - `SORTED TABLE` for ordered access and range-friendly keys.
  - `HASHED TABLE` for frequent exact key lookup.
- Sort before binary search and keep the sort key aligned with the read key.
- For `READ TABLE ... BINARY SEARCH`, compare the complete search-key order with the most recent valid ascending `SORT`; matching field sets in a different order are not sufficient.
- Treat standard-table sorting as mutable state. Any append, insert, assignment, or key-field modification after sorting can invalidate binary search.
- Do not accept one successful lookup as proof: a mismatched binary search can accidentally find some keys while missing others.
- Prefer explicit sorted/hashed table keys or secondary keys for stable repeated access instead of making callers maintain a hidden sort contract.
- Avoid repeated full-table scans in loops when a keyed table or pre-grouping is clearer.
- When reviewing performance, ask about data volume and access pattern before recommending a table type.
- Treat `SELECT ... INTO TABLE @DATA(...)` as a convenience that creates a standard table; if later code does frequent keyed reads, recommend an explicit table type.
- Prefer `LOOP AT ... ASSIGNING FIELD-SYMBOL(...)` when modifying large internal table rows and the direct mutation is clear.
- Do not recommend `HASHED TABLE` by default; use it for repeated exact key access with a suitable unique key.

## Transactions and Updates

- Be explicit about `COMMIT WORK` ownership.
- Do not place commits inside reusable helpers unless that is the agreed contract.
- When using BAPIs, check return messages and commit/rollback behavior deliberately.
- Consider idempotency for interfaces and reprocessing.

## Version Management and Recovery

- For ABAP code recovery tasks, check object version management before suggesting manual edits.
- Prefer `Retrieve` when restoring a clear historical version.
- After recovery, require a version diff, activation, regression test, and transport check.
- Do not reconstruct old code from memory, comments, screenshots, or chat history unless no versioned source exists.
- When current code contains valid later changes, call out the need for manual merge rather than a blind full rollback.
- When one change request is partially implemented and another change request must cut in, restore a clear baseline with version management before starting the urgent change; use diff to confirm only the intended partial change was removed.

## Authorization and Locks

- Do not treat `AUTHORITY-CHECK` as a formality. Confirm object, fields, organization levels, and failure handling.
- Use lock objects where concurrent updates can cause inconsistent data.
- Release locks predictably and handle lock failures in user-facing language.

## Output Expectations

When generating ABAP, include:

- Assumptions.
- Key tables/objects if known.
- Code snippet or pseudo-code.
- Error handling.
- Performance notes.
- Test checklist.


## Table Expression Line Existence

Use table expressions only when the row is expected to exist or when absence is handled deliberately.

- If absence is a normal business case, use `VALUE #( itab[ ... ] OPTIONAL )`, `DEFAULT`, `line_exists( )`, or explicit `READ TABLE` handling.
- If absence indicates inconsistent data, do not hide it with `OPTIONAL`; log the key and fail fast with a business exception or application log entry.
- Do not check `sy-subrc` after a table expression. Use `READ TABLE` when `sy-subrc` semantics are needed.

## Deduplicating Standard Tables

When reviewing `DELETE ADJACENT DUPLICATES`, treat sorting and comparison as one contract.

- Extract all `COMPARING` fields as the grouping key.
- Sort by the complete grouping key first so equal rows are adjacent.
- Put the retention fields after the grouping key, for example date or timestamp descending when the newest row must survive.
- Add a stable tie-breaker when retention fields can be equal.
- Remember that the statement keeps the first row of each adjacent duplicate group.
- Verify both the result row count and the exact retained row with minimal test data.
- Do not use deduplication when the requirement is aggregation or field merging.

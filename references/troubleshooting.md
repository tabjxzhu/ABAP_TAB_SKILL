# SAP TAB Troubleshooting Playbook

Use this reference for production issues, background jobs, dumps, interface failures, update errors, and performance diagnosis.

## Triage Order

1. Confirm the exact symptom.
2. Confirm scope: one user, one company code, one plant, one interface, all users, all data.
3. Confirm time window and whether anything changed recently.
4. Collect evidence before changing code.
5. Reproduce safely if possible.
6. Separate data issue, authorization issue, configuration issue, and code issue.

## Common Evidence

- ST22 dump.
- SM37 job log and spool.
- SM21 system log.
- SM13 update failures.
- SLG1 application log.
- SM58 transactional RFC.
- WE02/WE05 IDoc status.
- ST05 SQL trace.
- SAT/SE30 runtime analysis.
- SU53 and authorization trace when relevant.

## Background Job Failure

Check:

- Job variant and changed parameters.
- User and authorization context.
- Job log and spool.
- Dump at the same timestamp.
- Data volume changes.
- Locks or update failures.
- External interface dependencies.

Output should distinguish:

- Immediate recovery.
- Root cause analysis.
- Permanent fix.
- Prevention or monitoring.

## Background Job Slowdown

When a background job suddenly becomes slow, do not jump directly to SQL or code changes.

Check:

- Baseline runtime: normal duration, current duration, frequency, and affected time window.
- SM37 job details: job name, step, user, variant, start/end time, job log, and spool.
- Same-timestamp evidence: ST22, SM21, SM13, SLG1, interface logs, and external dependency logs.
- Input changes: selection parameters, variants, business data volume, master data changes, and recent transports.
- Runtime environment: application server, server group, batch window, concurrent heavy jobs, load balancing, and RFC/HTTP/file dependencies.
- Bottleneck tools: ST05, SAT/SE30, SQL Monitor, or explicit step-level runtime logging.

Output should separate:

- Immediate recovery: schedule, split data range, rerun, server group, or temporary dependency workaround.
- Root cause: data volume, SQL, ABAP loop, lock/update wait, remote call, server load, or external system response.
- Permanent fix: code defense, query/index change, job design, retry/error handling, monitoring, or alerting.
- Verification: compare with baseline runtime and record evidence.
## Performance Issue

Check:

- Whether data volume changed.
- SQL statements and indexes.
- Repeated SELECT inside loops.
- Internal table access pattern.
- Internal table type and key design: standard, sorted, hashed, primary key, secondary key.
- Repeated `READ TABLE` or `LOOP AT ... WHERE` inside large loops.
- Work area copying on large internal table updates; consider `ASSIGNING` where appropriate.
- Expensive conversions or function calls inside loops.
- Authorization or text retrieval repeated per row.
- Background job server and time window.

## BAPI or Interface Issue

Check:

- Return table messages.
- Commit/rollback behavior.
- Required fields and extension structures.
- Authorization.
- Lock conflicts.
- Idempotency and duplicate processing.
- Error log design for reprocessing.


## BAPI Success But No Data Update

When a user says a BAPI call succeeded but the business object was not updated, do not treat `sy-subrc = 0` as business success.

Check in this order:

1. Full RETURN table, including `A/E/W/I/S`, not only the first row.
2. Transaction ownership: current report, wrapper class, RFC caller, interface framework, or background job framework.
3. Whether `BAPI_TRANSACTION_COMMIT` is required, and whether `WAIT = 'X'` is needed before verification.
4. Whether the error branch calls `BAPI_TRANSACTION_ROLLBACK` and logs complete messages.
5. Locks and update failures: SM12, SM13, SLG1, ST22, and custom interface logs.
6. Required fields, X structures, update flags, control/header fields, extension structures, units, and currencies.
7. Business status, authorization, enhancements, BAdIs, and user exits that may reject or change the update.
8. Final verification through the business object state, not only through the RETURN table.
9. Idempotent retry and application-log design for interface or batch scenarios.

For code review, flag helpers that call commit internally unless the helper explicitly owns the LUW.

When AI generates a write-BAPI helper, require an explicit commit-owner decision before code generation. The owner is determined by the business atomicity unit, not by the function that happens to call the BAPI. A rollback cannot undo an earlier commit, and `WAIT = abap_true` changes waiting behavior rather than ownership.


## CX_SY_ITAB_LINE_NOT_FOUND

When ST22 shows `CX_SY_ITAB_LINE_NOT_FOUND`, first locate the table expression and decide whether the missing line is a valid business case or a data error.

Check:

1. ST22 exception class, source line, and active call stack.
2. The exact internal table and table expression key fields.
3. Triggering key values from job log, application log, selection screen, or debug data.
4. Source of the internal table: selection conditions, filters, aggregation, mapping, and key completeness.
5. Business semantics: missing row is allowed default, skipped item, or hard data inconsistency.
6. Fix choice: `READ TABLE`, `line_exists( )`, `VALUE #( ... OPTIONAL )`, `DEFAULT`, or fail-fast business exception.
7. Regression test with a missing key.

Do not blindly replace every table expression with `OPTIONAL`; it can hide data-quality issues.

## Dump Analysis

Do not jump straight to code changes.

Check:

- Exact exception and source line.
- Input data that triggered the dump.
- Recent transports.
- User/action/job that triggered it.
- Whether the same path worked before.
- Whether the issue is data-specific or general.

## SM13 Update Failure

Build the evidence chain in this order:

1. Filter by client, user, date, narrow time window, and `Error` status.
2. Record update key, transaction, update server, user, and creation time from Update Header.
3. Open Update Modules and identify the exact function module in `Error`; do not treat every unprocessed module as the root cause.
4. Capture the program element, source line, message class/number, error text, or short dump from module status.
5. Read Update Data and extract business keys and critical parameter values.
6. Inspect the failed update function source and follow wrappers, enhancements, SQL, and downstream calls.
7. Run where-used on the update function and use transaction, time, user, and business keys to select the real caller.
8. Before Repeat Update, classify the cause as transient infrastructure, deterministic code/data, or non-repostable V1; verify idempotency, lock assumptions, and external side effects.

Do not claim update success from asynchronous `COMMIT WORK` and `sy-subrc = 0`. `COMMIT WORK AND WAIT` waits for high-priority update processing only when the current caller legitimately owns the transaction boundary.

## Response Format

For troubleshooting answers, use:

1. What I would check first.
2. Why this is likely.
3. Evidence needed.
4. Possible fix.
5. Risk of the fix.
6. How to verify.

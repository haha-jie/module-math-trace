# Module Math Trace — Module Index

Read this index before starting or iterating on a module.

## Status meanings

- `in progress` — analysis has started; important steps remain.
- `statically analyzed` — inspected and derived from source, but not confirmed by runtime execution.
- `runtime verified` — relevant shapes or behavior were confirmed by an actual run or focused test; the record must name the command/test and outcome.
- `needs revalidation` — source/configuration changed or an earlier conclusion may no longer hold.

## Modules

| Module ID | Module / symbol | Source path | Record | Status | Last reviewed | Next step |
|---|---|---|---|---|---|---|
| — | No module records yet | — | — | — | — | Analyze a target module and add its record |

## Rules

- Use one record per module/symbol, with a stable ID based on repository-relative path and symbol.
- Keep this table concise; full derivations belong in individual records.
- Never mark a module `runtime verified` without recording the exact test/probe and result.
- When source files or configuration change, mark affected records `needs revalidation`.
- Do not record secrets, tokens, credentials, or unrelated personal data.

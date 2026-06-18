---
name: fix-diagnostics
description: Fixes targeted IDE, LSP, compiler, and Vale diagnostics with minimal edits and explicit re-validation. Use when diagnostics identify concrete issues in one or more files and the goal is to clear them without unrelated refactors.
disable-model-invocation: true
---

Fix only the requested diagnostics. Keep meaning and avoid unrelated cleanup.

## Default workflow

1. Inspect the reported diagnostics and group them by file and type.
2. Read the smallest relevant file region needed to understand each diagnostic.
3. Apply the narrowest fix that matches existing project patterns.
4. Re-run diagnostics for the changed file or the smallest relevant scope.
5. Stop when diagnostics clear or after 1–2 focused attempts per diagnostic.

Use this default loop unless the user asks for a broader refactor.

Progress checklist:

- [ ] Review diagnostics
- [ ] Read minimal context
- [ ] Apply focused fix
- [ ] Re-run diagnostics
- [ ] Report remaining blockers

## Scope rules

- Fix only targeted diagnostics.
- Preserve behavior unless the diagnostic itself requires a behavior change.
- Prefer existing imports, helpers, types, and patterns over new abstractions.
- Avoid renames, formatting churn, or opportunistic cleanup unless required.
- If a diagnostic is still unclear after 1–2 focused attempts, stop and report the blocker.

## Diagnostic handling

Use the smallest fitting strategy first:

- **Syntax/parser diagnostics:** fix malformed code, broken delimiters, malformed YAML/JSON/TOML, or indentation problems first.
- **Type/import diagnostics:** add missing imports, correct names, align signatures, or update types to match nearby code.
- **Config/schema diagnostics:** preserve structure, update only the incorrect field or value, and keep surrounding content unchanged.
- **Generated-code diagnostics:** prefer fixing the source input or generator usage rather than editing generated output directly unless the repository expects direct edits.

## Vale diagnostics

Treat Vale diagnostics as style or prose issues, not code issues.

Preferred handling order:

1. Rewrite the flagged text so it keeps the original meaning while satisfying the rule.
2. If the word is valid and domain-specific (a product name, tool, acronym, or proper noun), **add it to `.github/styles/Vocab/Base/accept.txt`** before considering any suppression.
3. If the wording is intentional but not a simple vocabulary entry, use the narrowest accepted suppression already used in the repository.
4. Only adjust Vale configuration or style rules when the repository owns the rule and the user is asking for that broader change.

For Vale diagnostics, prefer these alternatives in order:

- rewrite the sentence or heading
- replace only the flagged term or phrase
- add the term to `accept.txt` (valid domain word)
- add a local suppression for intentional wording that cannot be simplified
- update the repository Vale rule or vocabulary if that is the real source issue

Avoid broad disabling of Vale or blanket suppression comments unless explicitly requested.

## Validation and reporting

- Re-run diagnostics for the changed file first.
- Broaden validation only if the local diagnostic signal is not enough.
- If diagnostics clear, report the files changed and the checks re-run.
- If diagnostics remain, report the exact blocker, what you tried, and the next likely fix.

## Examples

### Example: type diagnostic

Input:

- `src/service.ts`: `Cannot find name 'usernmae'.`

Done:

- rename `usernmae` to `username`
- re-run diagnostics for `src/service.ts`
- report whether the file is now clean

### Example: Vale diagnostic — word substitution

Input:

- `docs/guide.md`: Vale flags `utilize`

Done:

- rewrite `utilize` to `use`
- re-run diagnostics for `docs/guide.md`

### Example: Vale diagnostic — domain vocabulary

Input:

- `docs/guide.md`: Vale flags `eBPF`

Done:

- add `eBPF` to `.github/styles/Vocab/Base/accept.txt`
- re-run diagnostics for `docs/guide.md`

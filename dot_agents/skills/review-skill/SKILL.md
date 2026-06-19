---
name: review-skill
description: Reviews and improves Zed agent skills (SKILL.md files) against Anthropic's authoring best practices. Use when asked to review, audit, lint, or improve an agent skill, or when checking a skill before sharing it.
---

# Review and Improve Agent Skills

Audit a SKILL.md (and its supporting files) against Anthropic's skill authoring best practices. Produce a structured review with actionable fixes.

## When NOT to use

- Skill is a rough draft the user is still actively writing — offer to review when they're ready.
- User only wants to fix a single known issue — just fix it directly.

## Review process

1. **Locate the skill.** Read the target `SKILL.md` and list any sibling files in its directory.
2. **Score each category** in the checklist below (✅ pass, ⚠️ needs work, ❌ fail).
3. **Summarise findings** as a markdown table with category, verdict, and one-line note.
4. **Propose fixes** for every ⚠️ and ❌, ordered by impact. Offer to apply them.

Copyable progress tracker:

```
Review progress:
- [ ] Read SKILL.md and list directory contents
- [ ] Score each checklist category
- [ ] Present findings table
- [ ] Propose fixes
- [ ] Apply fixes (if requested)
```

## Review checklist

### Frontmatter

- `name`: ≤64 chars, lowercase alphanumeric + single hyphens, matches directory name, no reserved words (`anthropic`, `claude`), no XML tags.
- `description`: non-empty, ≤1024 chars, no XML tags, third-person voice, states both **what** the skill does and **when** to use it, includes key trigger terms.

### Conciseness

- Every paragraph justifies its token cost — no explanations Claude already knows.
- SKILL.md body is under 500 lines.
- Detailed reference content is split into separate files, not inlined.

### Degrees of freedom

- Specificity matches task fragility: strict scripts for fragile ops, flexible guidance for context-dependent tasks.
- A sensible default is provided before alternatives ("use X; for Y edge case, use Z").
- No laundry-list of equivalent options without a recommended default.

### Progressive disclosure

- SKILL.md acts as a table of contents; heavy content lives in linked files.
- References are **one level deep** from SKILL.md (no chained file→file→file).
- Reference files >100 lines include a table of contents at top.
- File names are descriptive (`form_validation_rules.md`, not `doc2.md`).

### Structure and style

- Consistent terminology (one term per concept throughout).
- No time-sensitive information (or wrapped in an "old patterns" section).
- Forward-slash paths only (no backslashes).
- Examples are concrete input/output pairs, not abstract descriptions.

### Workflows

- Complex tasks are broken into numbered steps.
- Multi-step workflows include a copyable checklist for progress tracking.
- Feedback loops exist for quality-critical tasks (validate → fix → re-validate).

### Scripts (if applicable)

- Scripts handle errors explicitly rather than punting to Claude.
- No magic constants — every value is justified with a comment.
- Required packages are listed with install commands.
- Instructions clarify whether Claude should **execute** or **read** each script.

### Naming

- Skill name uses gerund form or clear noun/action phrase.
- Name is not vague (`helper`, `utils`, `tools`).

## Output format

Present the review as:

```
## Skill review: `<skill-name>`

| Category | Verdict | Note |
|---|---|---|
| Frontmatter | ✅ | … |
| Conciseness | ⚠️ | … |
| … | … | … |

### Recommended changes

1. **[Category]** Specific fix description.
2. …
```

After presenting the review, ask: "Want me to apply these fixes?"

### Example review snippet

For a skill with an overly vague description and no workflow checklist:

```
## Skill review: `deploy-helper`

| Category | Verdict | Note |
|---|---|---|
| Frontmatter | ⚠️ | Description says "helps with deployments" — too vague for discovery |
| Workflows | ❌ | 8-step deploy process has no checklist and no rollback loop |

### Recommended changes

1. **[Frontmatter]** Rewrite description: "Deploys services to Kubernetes via Helm,
   validates pod health, and rolls back on failure. Use when deploying, promoting,
   or rolling back a service."
2. **[Workflows]** Add copyable checklist and a validate→rollback feedback loop
   after the `helm upgrade` step.
```

## Applying fixes

When fixing, follow these rules:

- Preserve the author's intent and domain knowledge.
- Remove only what is genuinely redundant for Claude.
- Tighten descriptions to be specific and third-person.
- Restructure into separate files only when SKILL.md exceeds ~300 lines or has clearly separable reference content.
- Add a progress-tracking checklist to any multi-step workflow that lacks one.
- Never remove concrete examples, scripts, or domain-specific rules.

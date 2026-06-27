# Global agent instructions

## Communication

- Be concise, direct, and technical.
- Prefer actionable summaries over long explanations.
- Call out uncertainty and validation gaps.

## Code changes

- Make minimal, focused changes.
- Preserve repository style and existing patterns.
- Do not rewrite unrelated code or discard user work unless explicitly asked.
- Prefer repository-native patterns over new dependencies.

## Terminal and validation

- Prefer repository-native tools and workflows.
- Avoid using `python` and `pipx` unless there is no other way.
- Bound long-running commands with timeouts.
- Run the narrowest relevant check first, then broaden if useful.
- State exactly what you ran and whether it passed.
- Do not claim validation you did not run.

## Git and commits

- Do not commit, push, rebase, or create branches unless explicitly asked.
- When asked for a commit message, return only the commit message.
- Use Conventional Commits unless the repository uses another convention: `<type>(optional scope): <description>`.
- Keep subjects lowercase where appropriate, imperative, without trailing punctuation, and ≤72 chars.
- Add a body only when it adds necessary context; wrap at 72 chars and avoid repetition.
- Use `!` and a `BREAKING CHANGE:` footer for breaking changes.
- Put issue references in footers, e.g. `Fixes #123`.

## Plans

- For multi-step tasks, create a short plan before editing.
- Keep plans terse; use checklists or status tables when they help.
- For implementation plans, decide whether one commit or more is appropriate.
- Never commit `.agents/plans/**` unless explicitly asked.
- Always update the active plan before ending the turn.

## Branches

When on a branch:

- Prefer `git commit --fixup` to fix a previous commit instead of adding a `fix` commit on top
- When unsure which commit introduced the behavior, inspect history before deciding.

# SDD with Cursor

> How to practice Spec-Driven Development with Cursor IDE.

Last verified: 2026-04-04

---

### Project Rules (.cursorrules)

**Why**: Cursor reads `.cursorrules` on every session -- this is your persistent constraint layer. It shapes every response the AI produces, making it the single most important file for SDD compliance in Cursor.

**How**:
- Create `.cursorrules` at the repo root.
- Include: coding standards, framework rules, file structure conventions, "never do" rules, testing requirements.
- Keep rules concrete and testable (e.g., "All API handlers must return typed responses" not "Write good code").
- Revisit `.cursorrules` when your spec evolves -- stale rules cause drift.

Source: [Cursor docs -- Rules for AI](https://docs.cursor.com/context/rules-for-ai)

---

### Context Files (@file References)

**Why**: Cursor's `@file` lets you explicitly feed spec documents as context. This gives the AI direct access to the authoritative spec rather than relying on its memory or your summary.

**How**:
- Write specs as markdown files in your repo (e.g., `specs/feature-name.md`).
- Reference them in prompts: `@specs/feature-name.md implement the search feature as specified`.
- Use `@folder` to include an entire spec directory when multiple specs are relevant.
- Pin frequently-used spec files so they persist across prompts within a session.

Source: [Cursor docs -- @-symbols](https://docs.cursor.com/context/@-symbols)

---

### Agent Mode vs Composer

**Why**: Different modes suit different SDD phases. Using the wrong mode at the wrong time leads to either premature implementation or unnecessary back-and-forth.

**How**:
- Use **Composer** (conversational mode) for spec writing and planning. Iterate on requirements, ask clarifying questions, refine constraints.
- Use **Agent Mode** for implementation. It has autonomous tool use (file creation, terminal commands, multi-step edits) and works best when given a clear spec to follow.
- Use **inline edits** (Cmd+K) for targeted fixes within generated code -- small corrections that do not require a full agent session.

Source: [Cursor docs -- Agent Mode](https://docs.cursor.com/agent)

---

### .cursorignore for Spec Protection

**Why**: Prevent Cursor from modifying spec files during implementation. Specs are the source of truth -- they should change through deliberate decisions, not as a side effect of code generation.

**How**:
- Add spec directories to `.cursorignore` (e.g., `specs/`, `docs/architecture/`).
- This prevents Agent Mode from editing or deleting spec files.
- When you need to update a spec, do it manually or in a separate Composer session focused on spec revision.

Source: [Cursor docs -- Ignore files](https://docs.cursor.com/context/ignore-files)

---

### Notepads for Spec Templates

**Why**: Cursor Notepads let you store reusable templates and reference material that can be pulled into any chat with `@notepad`.

**How**:
- Create a Notepad with your spec template (sections, required fields, quality criteria).
- Create a Notepad with your project's architectural principles.
- Reference them when starting a new spec: `@spec-template create a spec for the new authentication flow`.

Source: [Cursor docs -- Notepads](https://docs.cursor.com/context/notepads)

---

### Recommended Workflow

1. Write the spec as a markdown file in your repo's spec directory.
2. Set up `.cursorrules` with project constraints and SDD rules.
3. Use Composer to refine the spec -- iterate conversationally until the spec is stable.
4. Reference the spec via `@file` in Agent Mode for implementation.
5. Generate tests first -- they encode the spec as executable assertions.
6. Generate implementation code.
7. Verify output against the spec. Use inline edits for small corrections.

---

### Known Limitations

- No built-in plan mode -- use Composer conversation to simulate the plan phase.
- `.cursorrules` is "best effort" -- it influences but does not guarantee AI behavior. Treat it as a strong nudge, not an enforcement mechanism.
- YOLO mode disables approval gates for terminal commands -- avoid this when practicing SDD, as it removes a critical review step.
- Context window limits mean very large specs may be truncated. Break large specs into focused sub-specs.

Source: [Cursor docs -- Known limitations](https://docs.cursor.com/troubleshooting)

---

### Checklist Summary

- [ ] `.cursorrules` exists at repo root with project constraints and SDD rules.
- [ ] Specs are stored as markdown files in a dedicated directory.
- [ ] `.cursorignore` protects spec files from AI modification.
- [ ] Composer is used for spec writing; Agent Mode for implementation.
- [ ] Specs are referenced via `@file` in all implementation prompts.
- [ ] Tests are generated before implementation code.
- [ ] Notepads contain spec templates and architectural principles.
- [ ] Spec changes are deliberate and tracked, never a side effect of code generation.

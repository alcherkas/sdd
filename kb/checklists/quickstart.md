# Quick Start: Minimum Viable SDD

> Last verified: 2026-04-04

> These 10 actions provide immediate improvement in AI-generated code quality regardless of which AI coding agent you use. Start here, then move to the [tool-specific guides](.) and [full documentation](../docs/).

## The 10 Essential Actions

### 1. Write Requirements Before Prompting
**Why**: Unstructured prompts produce unpredictable output. A brief spec (even 5 sentences) anchors the AI to your intent.
**How**: Before opening your AI tool, write: Goal (1 sentence), Core Requirements (3-5 bullets), Success Criteria (how you'll know it works).

### 2. Define Interfaces Before Internals
**Why**: LLMs produce better code when contracts are explicit. Ambiguous interfaces cause cross-component drift.
**How**: Write type signatures, API schemas, or function signatures FIRST. Feed these to the AI as constraints.

### 3. Set Up Project Instructions
**Why**: Project-level instructions persist across sessions, ensuring consistent behavior.
**How**:
- Claude Code: Create `CLAUDE.md` at repo root
- Cursor: Create `.cursorrules` at repo root
- Copilot: Create `.github/copilot-instructions.md`

### 4. Include "Never Do" Rules
**Why**: AI agents will do anything plausible unless explicitly told not to. Prevent common mistakes upfront.
**How**: Add to your project instructions: libraries to avoid, patterns prohibited, security constraints.

### 5. Use Plan Mode for Exploration
**Why**: Plan mode helps you think through requirements before committing to implementation.
**How**:
- Claude Code: Press Shift+Tab or type `/plan`
- Kiro: Use the Requirements phase
- BMAD: Use the Analyst agent

### 6. Decompose Large Features into Focused Tasks
**Why**: LLMs perform better on bounded tasks. One component per generation session.
**How**: Break features into tasks of <200 LOC each. Implement in dependency order.

### 7. Include Test Criteria in Every Spec
**Why**: Without test criteria, you have no way to verify the output matches intent.
**How**: For each requirement, add "Verify: [specific testable condition]".

### 8. Generate Tests Before Implementation
**Why**: Tests from the spec define expected behavior. Code is generated to make tests pass.
**How**: Feed spec to AI with instruction: "Generate tests for these requirements first, then implement."

### 9. Verify Every Generation Against the Spec
**Why**: Skipping verification is the #1 cause of SDD failure. Trust, but verify.
**How**: After generation, review output against each spec requirement. Mark requirements as met/unmet.

### 10. Update Specs When Reality Diverges
**Why**: Specs that drift from reality are worse than no specs -- they create false confidence.
**How**: When verification reveals spec gaps, update the spec FIRST, then regenerate.

## What's Next?

After adopting these 10 actions:
1. Use the [Spec Review Checklist](spec-review.md) before every generation session
2. Read the full documentation in [docs/](../docs/) for deeper coverage of each practice
3. Follow tool-specific guides for your agent in [checklists/](.)

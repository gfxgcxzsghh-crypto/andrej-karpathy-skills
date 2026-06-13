# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

---

# Repository Guide (for AI assistants working *in* this repo)

> The section above (the four principles) is the **product** this repository ships. The notes below are about maintaining the repo itself — they are not part of the distributed guidelines.

## What this repository is

A **content-distribution repo**, not an application. There is no source code, build step, test suite, or dependency manifest. It packages a single set of Karpathy-inspired behavioral guidelines ([source post](https://x.com/karpathy/status/2015883857489522876)) and ships the *same content* through four delivery channels.

## Layout

| Path | Role |
|------|------|
| `CLAUDE.md` | Root instruction file. Users `curl`/copy it into their own projects. **This file is itself the product** — its four principles are the canonical text. |
| `skills/karpathy-guidelines/SKILL.md` | Agent Skill body. Has YAML frontmatter (`name`, `description`, `license`) plus the four principles. Used by the Claude Code plugin and as a standalone skill. |
| `.cursor/rules/karpathy-guidelines.mdc` | Cursor project rule. YAML frontmatter (`description`, `alwaysApply: true`) plus the four principles. |
| `.claude-plugin/plugin.json` | Plugin manifest. `skills` array points at `./skills/karpathy-guidelines`. |
| `.claude-plugin/marketplace.json` | Marketplace manifest (`id: karpathy-skills`) that publishes the plugin. |
| `README.md` / `README.zh.md` | English / Simplified-Chinese docs. Must stay in sync with each other. |
| `CURSOR.md` | Cursor setup instructions and the contributor sync note. |
| `EXAMPLES.md` | Before/after examples illustrating the four principles. |

## The core invariant: keep the four copies in sync

The four principles exist verbatim (modulo each file's frontmatter/wrapper) in **four places**:

1. `CLAUDE.md`
2. `skills/karpathy-guidelines/SKILL.md`
3. `.cursor/rules/karpathy-guidelines.mdc`
4. (described in) `README.md` + `README.zh.md`

**When you change a principle, update all of them in the same change.** A drift between these files is the most common bug in this repo. After editing, diff the principle text across the three machine-read files to confirm they match.

## Conventions

- **Markdown only.** No code to lint or test. "Verification" here means: principle text is identical across files, links resolve, and any JSON manifest is valid (`plugin.json`, `marketplace.json`).
- **Bilingual docs.** Any change to `README.md` should be mirrored in `README.zh.md` (and vice versa). Recent history shows these are explicitly synced (e.g. the Cursor section).
- **Frontmatter matters.** `SKILL.md` needs its `name`/`description`/`license` block; the `.mdc` rule needs `description` + `alwaysApply: true`. Don't drop frontmatter when copying principle text between files.
- **Version bumps.** `plugin.json` and `marketplace.json` both carry `version: 1.0.0`. Bump them together when releasing.
- Follow the repo's own four principles when editing: surgical changes, no speculative additions, surface tradeoffs.

## Workflow

This repo has no CI, build, or test commands. Development is: edit Markdown/JSON → keep the four channels in sync → commit → open a PR. Changes are validated by review, not automation.

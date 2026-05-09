---
name: coding-discipline
description: |
   MANDATORY: Load this skill BEFORE any coding task — writing code, editing files, fixing bugs, adding features, refactoring, deploying, or managing versions. This skill is NOT optional. It enforces version tracking, changelog updates, git commits with version prefixes, and surgical modifications. If you are about to modify any source file, create a new file, or make a git commit, you MUST invoke this skill first. Even if the task seems trivial or you think you already know the workflow, load this skill. Skipping it will result in missing changelog entries, incorrect version numbers, and uncommitted changes — problems that have repeatedly occurred when this skill was bypassed. Trigger this skill proactively on EVERY coding-related user message, no exceptions.
---

# Coding Discipline

This skill establishes a set of behavioral rules that govern how you approach software engineering work. These rules exist because LLMs have predictable failure modes — over-engineering, scope creep, unnecessary refactoring, and drifting away from user intent. Following these rules produces better outcomes with less waste.

## Rule 1: Think Before You Code

Before writing any code, pause and clarify the requirement. LLMs tend to speculate when instructions are ambiguous, producing code that misses the mark. When a request is unclear:

- State your understanding of the requirement explicitly
- Identify what's ambiguous and ask the user to clarify
- Do NOT assume implementation details the user didn't specify
- Do NOT start coding until you can articulate what "done" looks like

The cost of asking is always lower than the cost of building the wrong thing.

## Rule 2: Keep It Simple

Implement only what the current task requires — nothing more. LLMs gravitate toward over-engineering: a simple discount function becomes a strategy pattern with a factory. Resist this.

- If 50 lines solve the problem, do not write 200 lines
- Do not add abstractions "for future flexibility" unless the user asks
- Do not introduce design patterns unless the complexity genuinely warrants them
- Prefer plain functions over classes when state management isn't needed
- The simplest code that correctly solves the problem is the best code

## Rule 3: Surgical Modifications

When modifying existing code, change only what must be changed. Every line you touch is a line that can break.

- Do not reformat adjacent code "while you're in the area"
- Do not refactor working code that isn't related to the task
- Do not rename variables or reorganize imports unless specifically asked
- Each modification should be traceable to the user's request
- If you see something unrelated that bothers you, note it but don't fix it unless asked

## Rule 4: Goal-Oriented Execution

Break tasks into verifiable goals, then execute them in order. "Add validation" is vague; "write a test for invalid input, then make it pass" is a goal.

- Decompose the task into specific, testable objectives
- Execute one goal at a time
- Verify each goal before moving to the next
- If a goal fails, debug it before proceeding — don't accumulate broken state
- When all goals are met, the task is done — don't add extra work

## Rule 5: Project Initialization — Git, Release Notes, Product Definition

When starting a new project or working in a project that lacks these files, create three foundational files:

### 5.1 Git Repository
Initialize a git repository (`git init`) if one doesn't exist. All work should be tracked.

### 5.2 Release Notes (`releasenote.md`)
Create a `releasenote.md` file at the project root. This file records every change made to the project. Each entry includes:
- Version number (see Rule 6 for versioning rules)
- Date
- Description of the change
- Type of change (feature / requirement / bugfix)

Format:
```
## [x.x.x] - YYYY-MM-DD
### Type: feature | requirement | bugfix
- Description of the change
```

### 5.3 Product Definition (`product.md`)
Create a `product.md` file at the project root. This file contains the **latest** product definition and requirements only. It is a living document that reflects the current state of what the product is and what it should do.

- Only update this file when the user's new requirement **conflicts** with the existing product definition
- If the new requirement is additive (doesn't contradict existing definitions), add it
- If the new requirement contradicts the existing definition, replace the conflicting parts — do not keep both versions
- This file should always represent the single source of truth for what the product is right now

## Rule 6: Semantic Versioning

All changes are tracked with version numbers following `MAJOR.MINOR.PATCH` format:

| Position | Name   | When to increment                                    |
|----------|--------|------------------------------------------------------|
| First    | MAJOR  | When the user explicitly instructs a major upgrade   |
| Second   | MINOR  | When the user provides a new requirement (new feature / new behavior) |
| Third    | PATCH  | When fixing a bug or resolving an issue              |

Rules:
- Only one position increments per change; lower positions reset to 0
  - Example: 1.2.3 → new requirement → 1.3.0 (not 1.3.3)
  - Example: 1.2.3 → bugfix → 1.2.4
  - Example: 1.2.3 → major upgrade → 2.0.0
- Every commit / change must reference its version number
- The version in `releasenote.md` is the source of truth for the current version

## Rule 7: Commit Discipline

Every code change must be accompanied by a commit with a comment that:
- References the version number from `releasenote.md`
- Briefly describes what was changed and why

Format:
```
[vX.X.X] Brief description of the change
```

Example:
```
[v1.2.0] Add user authentication with JWT tokens
[v1.2.1] Fix token expiration check causing premature logout
```

## Workflow Summary

When you receive a coding task, follow this sequence:

1. **Clarify** — What is the requirement? (Rule 1)
2. **Check project files** — Does the project have `git`, `releasenote.md`, `product.md`? If not, create them. (Rule 5)
3. **Determine version** — Is this a new requirement, a bugfix, or a major upgrade? Update `releasenote.md` accordingly. (Rule 6)
4. **Check product definition** — Does this change conflict with `product.md`? Update if needed. (Rule 5.3)
5. **Plan minimally** — What's the simplest implementation? (Rule 2)
6. **Execute surgically** — Make only the necessary changes. (Rule 3)
7. **Verify** — Does the change meet the goal? (Rule 4)
8. **Commit** — Commit with version-tagged message. (Rule 7)
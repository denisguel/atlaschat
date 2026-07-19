---
name: engineering
description: Governs all software engineering practices for ATLAS. Ensures implementations are maintainable, modular, testable, and production-ready while preventing technical debt.
priority: high
version: 1.0
status: FROZEN
---

# MISSION

Produce production-quality software.

Every implementation must improve or preserve the quality of the codebase.

Working code is not enough.

Maintainable code is required.

---

# SINGLE RESPONSIBILITY

Define how software is implemented.

Nothing else.

---

# INPUTS

- Approved implementation request
- Existing source code
- Architecture rules
- Current module
- Existing interfaces

---

# OUTPUTS

One of four outcomes only.

- IMPLEMENTATION APPROVED
- IMPLEMENTATION APPROVED WITH WARNINGS
- REFACTOR RECOMMENDED
- IMPLEMENTATION REJECTED

Every outcome must explain why.

---

# IMPLEMENTATION WORKFLOW

Execute this sequence.

## Step 1

Understand the objective.

Never start coding immediately.

---

## Step 2

Locate existing implementation.

Always search before creating.

Prefer reuse.

---

## Step 3

Determine ownership.

Every modification belongs to exactly one module.

Never spread logic across unrelated modules.

---

## Step 4

Implement the smallest solution that completely solves the problem.

Avoid overengineering.

---

## Step 5

Validate architecture.

Never introduce coupling.

Never duplicate business logic.

---

## Step 6

Run validation checklist.

Only then finish.

---

# ENGINEERING PRINCIPLES

Prefer existing code over new code.

Prefer reuse over duplication.

Prefer readability over cleverness.

Prefer explicit behavior over implicit behavior.

Prefer small functions.

Prefer cohesive modules.

Prefer deterministic behavior.

Optimize only when necessary.

Remove complexity whenever possible.

---

# CODE RULES

Write self-explanatory code.

Avoid unnecessary comments.

Avoid dead code.

Avoid unused dependencies.

Avoid large functions.

Avoid deep nesting.

Avoid magic numbers.

Avoid duplicated constants.

Avoid hidden side effects.

---

# MODIFICATION RULES

Modify existing code when appropriate.

Create new code only when reuse is impossible.

Never rewrite working modules without measurable benefit.

Never introduce breaking changes unless explicitly approved.

---

# DEPENDENCY RULES

Minimize dependencies.

Prefer project standards.

Never introduce a new dependency without clear justification.

Remove obsolete dependencies whenever safe.

---

# ERROR HANDLING

Every failure must:

Be predictable.

Be recoverable.

Return meaningful information.

Never fail silently.

Never hide exceptions.

---

# PERFORMANCE

Optimize only after correctness.

Never sacrifice maintainability for micro-optimizations.

Measure before optimizing.

---

# VALIDATION CHECKLIST

Before approving verify:

- objective fulfilled
- architecture preserved
- module ownership respected
- no duplicated logic
- no dead code
- no unnecessary abstraction
- no hidden dependency
- no breaking interface
- implementation remains readable

If any item fails:

Reject.

---

# AUTOMATIC REJECTION RULES

Reject immediately if the implementation:

- duplicates existing functionality
- bypasses architecture
- mixes responsibilities
- creates technical debt
- introduces unnecessary complexity
- adds undocumented dependencies
- modifies unrelated modules
- leaves unused code

---

# REFACTOR RULES

Recommend refactoring only if it:

Reduces complexity.

Improves maintainability.

Eliminates duplication.

Improves readability.

Never refactor only for style.

---

# DOCUMENTATION

Update documentation only if:

Public behavior changes.

Architecture changes.

Public interfaces change.

Never rewrite documentation unnecessarily.

---

# INTERACTION

Architecture defines structural limits.

Decision Engine validates reasoning.

Engineering performs implementation.

Quality validates the result.

Hospitality validates business behavior.

---

# FORBIDDEN ACTIONS

Never:

Implement before understanding.

Duplicate code.

Introduce temporary hacks.

Ignore existing patterns.

Optimize prematurely.

Hide technical debt.

Rewrite modules unnecessarily.

Change project conventions.

---

# DEFINITION OF DONE

Implementation is complete only if:

- objective achieved
- architecture preserved
- maintainability preserved
- readability preserved
- duplication avoided
- interfaces stable
- code production-ready

If every condition is satisfied:

Return

IMPLEMENTATION APPROVED
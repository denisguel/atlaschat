---
name: quality
description: Validates every implementation before completion. Ensures correctness, reliability, maintainability, security, and production readiness.
priority: high
version: 1.0
status: FROZEN
---

# MISSION

Protect the quality of ATLAS.

No implementation is considered complete until it passes quality validation.

Quality is mandatory.

---

# SINGLE RESPONSIBILITY

Validate implementation quality before completion.

Nothing else.

---

# INPUTS

- Modified source code
- Test results
- Documentation updates
- Implementation summary
- Architecture validation

---

# OUTPUTS

One of five outcomes only.

- QUALITY APPROVED
- QUALITY APPROVED WITH WARNINGS
- REWORK REQUIRED
- TESTING REQUIRED
- RELEASE BLOCKED

Every result must explain why.

---

# QUALITY WORKFLOW

Execute this sequence.

## Step 1

Verify objective.

Confirm the implementation solves the requested problem.

---

## Step 2

Verify architecture.

Confirm Architecture Skill approved the implementation.

---

## Step 3

Verify engineering quality.

Confirm:

- readable code
- maintainable code
- modular implementation
- stable interfaces

---

## Step 4

Verify business integrity.

Confirm Hospitality rules remain valid.

---

## Step 5

Verify regression risk.

Determine whether existing functionality may have been affected.

---

## Step 6

Verify documentation.

Confirm required documentation has been updated when applicable.

---

## Step 7

Determine final quality status.

---

# QUALITY PRINCIPLES

Correctness before performance.

Reliability before optimization.

Maintainability before convenience.

Consistency before speed.

Production readiness before feature completeness.

---

# VALIDATION CHECKLIST

Before approving verify:

- objective completed
- architecture preserved
- no duplicated logic
- interfaces remain compatible
- business rules preserved
- no regressions detected
- documentation updated when required
- implementation understandable
- implementation maintainable

If any item fails:

Reject.

---

# TESTING RULES

Verify appropriate testing exists.

Confirm:

- critical paths validated
- edge cases considered
- failure scenarios handled
- error handling verified

Never approve critical functionality without validation.

---

# SECURITY RULES

Confirm implementation does not:

Expose secrets.

Leak sensitive information.

Bypass authorization.

Trust unvalidated input.

Introduce obvious vulnerabilities.

If any issue exists:

Release Blocked.

---

# DOCUMENTATION RULES

Documentation is required only when:

Architecture changes.

Public behavior changes.

Public APIs change.

Operational procedures change.

Avoid unnecessary documentation updates.

---

# RELEASE CRITERIA

Approve release only if:

Architecture approved.

Decision validated.

Engineering approved.

Business rules preserved.

Quality checklist completed.

Regression risk acceptable.

---

# AUTOMATIC REJECTION RULES

Reject immediately if:

Tests fail.

Critical functionality broken.

Architecture violated.

Business logic duplicated.

Regression detected.

Security issue detected.

Documentation intentionally ignored.

Implementation incomplete.

---

# ESCALATION RULES

Return:

REWORK REQUIRED

when implementation quality is insufficient.

Return:

RELEASE BLOCKED

when production risk is unacceptable.

---

# INTERACTION

Architecture validates structure.

Decision Engine validates reasoning.

Engineering validates implementation.

Hospitality validates business behavior.

Quality performs final approval.

Quality is the final gate before completion.

---

# FORBIDDEN ACTIONS

Never:

Approve incomplete work.

Ignore failed validations.

Ignore regressions.

Ignore security issues.

Ignore architectural violations.

Ignore missing documentation when required.

Prioritize delivery over quality.

---

# DEFINITION OF DONE

Implementation is complete only if:

- objective achieved
- architecture preserved
- reasoning validated
- implementation maintainable
- business rules preserved
- regression risk acceptable
- security acceptable
- documentation complete when required

If every condition is satisfied:

Return

QUALITY APPROVED
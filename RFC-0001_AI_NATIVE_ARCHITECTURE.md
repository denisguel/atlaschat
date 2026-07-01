# RFC-0001_AI_NATIVE_ARCHITECTURE.md

---
id: RFC-0001
title: AI Native Architecture
status: canonical
version: 1.0.0
type: RFC
owner: Architecture
---

# 1. Context

Traditional hospitality software is built around systems where:

- UI drives logic
- Backend executes requests
- Data is passive
- Automation is external

AI is typically added as an optional layer.

This leads to fragmented intelligence, inconsistent decisions, and manual orchestration.

ATLAS is designed under a different assumption:

> Intelligence is the system. Software is the execution layer.

---

# 2. Decision

ATLAS adopts an **AI-Native Architecture**.

This means:

## 2.1 Core Rule

All meaningful system behavior is mediated by AI agents.

No critical business decision is executed without agent interpretation.

---

## 2.2 System Flow

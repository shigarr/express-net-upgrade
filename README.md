# Express Migration Control Repository

## Purpose

This repository serves as the **control layer** for the migration of the Express application from .NET Framework 4.8 to .NET 10.

It is **not a code repository**.

It exists to provide:

* Persistent context across AI-assisted sessions
* Deterministic decision-making
* Structured migration governance
* Traceability of risks, assumptions, and outcomes

---

## Scope

This repository contains **non-sensitive, non-proprietary artefacts only**, including:

* Migration planning documents
* System structure summaries
* Architectural decisions
* Risk assessments
* Migration patterns
* Execution tracking (anchor file)

No source code from the corporate repository is stored here.

---

## Key Principles

### 1. Separation of Concerns

* **Work Repository** → Source code (restricted)
* **Control Repository (this repo)** → Context, decisions, governance

---

### 2. Deterministic AI Collaboration

All AI interactions are governed by:

* Explicit context (no hidden assumptions)
* Structured prompts
* Repeatable patterns

This avoids:

* Context loss between sessions
* Inconsistent guidance
* Undocumented decisions

---

### 3. Minimal-Change Migration

The migration strategy focuses on:

* Preserving existing behaviour
* Avoiding unnecessary refactoring
* Upgrading only what is required for .NET 10 compatibility

---

### 4. Governance First

All key aspects are tracked:

* Decisions (`decisions.md`)
* Risks (`risks.md`)
* Current state (`anchor.md`)
* System structure (`solution-structure.md`)

---

## Repository Structure

```text
/express-migration-control/
  ├── README.md
  ├── anchor.md
  ├── solution-structure.md
  ├── migration-plan.md
  ├── governance.md
  ├── decisions.md
  ├── risks.md
  └── patterns/
        ├── config.md
        ├── api-controller.md
        ├── windows-service.md
        └── logging.md
```

---

## anchor.md (Critical File)

This is the **single source of truth for current state**.

It must always reflect:

* Current phase
* What has been completed
* What is next
* Active risks
* Key decisions

Every new AI session starts by referencing this file.

---

## How to Use with AI Agent

At the start of each new session:

1. Load the Agent Prompt
2. Paste the contents of `anchor.md`
3. Optionally include relevant supporting documents

Example:

```text
You are Express Migration Agent.

Here is the current context:
[paste anchor.md]
```

---

## Working Model

This project follows a **snippet-driven migration approach**:

* Code is never fully shared
* Only relevant snippets are analysed
* Solutions are pattern-based and reusable

---

## Constraints

* No proprietary code is stored here
* No direct integration with corporate repository
* All inputs must be manually curated

---

## Success Criteria

This repository is successful if it enables:

* Consistent decision-making across sessions
* Controlled and traceable migration progress
* Reduced risk during framework upgrade
* Clear communication with stakeholders

---

## Ownership

* Owner: Ali (Migration Lead)
* Supporting Role: AI Agent (Architect guidance)

---

## Final Note

This repository is a **control system**, not documentation.

If it is not actively maintained, it loses its value.

Discipline in updating `anchor.md` and tracking decisions is essential for success.

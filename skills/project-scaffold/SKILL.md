---
name: project-scaffold
description: >-
  Automatically scaffolds and initializes new project workspaces with the mandatory Antigravity persistent state architecture (root AGENTS.md, docs/agent/{SESSION_STATE,TASKS,HANDOFF}.md, clean Cargo/repo setup) before any production code is written.
---

# Project Scaffolding & Initial Architecture Skill

This skill enforces and automates the **mandatory initial scaffolding** of any new project, workspace, crate, or application across the Antigravity ecosystem.

It guarantees that every repository begins on rock-solid foundations:
1. Universal software engineering rules (TDD, 5-phase continuous lifecycle, merciless local audit, commit gates) are inherited **globally**.
2. The repository acts as its own **persistent, reproducible memory** across independent agent sessions.
3. Domain-specific constraints (e.g. `no_std`, real-time audio, safety invariants, language mandate) are declared upfront.

---

## 1. When to Use (Mandatory Trigger)

You MUST trigger this skill:
- When the user asks to start, create, or bootstrap a new project, library, CLI, or application.
- Whenever working in an existing codebase that lacks a root `AGENTS.md` or `docs/agent/` directory.
- **Rule of Precedence**: You are strictly FORBIDDEN from writing domain code, business logic, or algorithmic implementations before this scaffolding is in place and committed.

---

## 2. Standard Scaffolding Checklist

Every new project MUST contain the following 5 foundational files:

### File 1: Root `AGENTS.md`
The single entry point for any human or agent resuming work. It MUST:
1. State that global engineering standards (`idiomatic-craft`, `tiered-router`, `efficiency-router`) apply automatically.
2. Provide clickable links to `docs/agent/SESSION_STATE.md`, `HANDOFF.md`, and `TASKS.md`.
3. Document project-specific domain constraints (language, safety, architecture).

*Template:*
```markdown
# AGENTS.md — <Project Name> Workspace & Domain Guide

Welcome to the **<Project Name>** workspace.

> **Global Engineering Standards Apply Automatically**:  
> All engineering methodology (strict TDD, 5-phase continuous execution tracking, zero-panic/unwrap prohibition, mandatory merciless local audit via LM Studio, Headroom context compression, and atomic Git commit gates) are **enforced globally** by Antigravity rules (`idiomatic-craft`, `tiered-router`, `efficiency-router`).

---

## 1. Domain & Workspace Architecture

Before working on the codebase, review the domain-specific documentation:
1. `docs/agent/SESSION_STATE.md` — Current verified state of the workspace and toolchain status.
2. `docs/agent/HANDOFF.md` — Next tasks and context left by previous sessions.
3. `docs/agent/TASKS.md` — Milestone roadmap and backlog.

---

## 2. Project-Specific Constraints (<Project Name> Only)

- **Language Mandate**: 100% English repository (code, comments, tests, commit messages, and docs).
- **Core Safety & Isolation**: <e.g. #![forbid(unsafe_code)] or #![no_std]>.
- **Domain Invariants**: <e.g. zero heap allocations on real-time threads, lock-free queues, strict bounds>.
```

---

### File 2: `docs/agent/SESSION_STATE.md`
Tracks verified toolchain health, active milestones, and commit records.

*Template:*
```markdown
# Session State — <Project Name>

- **Date**: <YYYY-MM-DD>
- **Active Milestone**: Phase 1 — Foundational Architecture & Baseline Setup
- **Toolchain Status**:
  - Compiler / Tests: Clean (all tests passing)
  - Linter: Clean (0 clippy / linter warnings)
  - Working Tree: Clean
- **Architecture Overview**: <Brief summary of active crates and modules>
```

---

### File 3: `docs/agent/TASKS.md`
The milestone roadmap and decomposed tasks.

*Template:*
```markdown
# Tasks & Milestone Roadmap — <Project Name>

## Phase 1: Foundational Setup & Architecture (In Progress)
- [ ] Initialize Cargo workspace and crate manifests
- [ ] Establish public API contracts and traits
- [ ] Implement baseline deterministic unit tests

## Phase 2: Core Implementation
- [ ] <First feature milestone>
```

---

### File 4: `docs/agent/HANDOFF.md`
The exact next step left for the subsequent session.

*Template:*
```markdown
# Handoff — <Project Name>

## Summary of Completed Work
- Initialized repository scaffolding and verified global governance alignment.

## Next Logical Action
Begin **Phase 1 Implementation**:
- Define domain traits and write failing unit tests (TDD Red phase).
```

---

### File 5: `.gitignore`
Must include standard ignores:
```gitignore
/target/
/.headroom/
*.DS_Store
*.log
```

---

## 3. Execution Procedure

1. **Scaffold Directory Structure**:
   ```bash
   mkdir -p docs/agent
   ```
2. **Write the 5 Scaffolding Files** using the templates above.
3. **Initialize Git Repository** (if not already a git repository):
   ```bash
   git init
   ```
4. **Run Native Validation**:
   Ensure `cargo check` / `cargo test` runs cleanly.
5. **Initial Commit Gate (via `ask_question`)**:
   Display the scaffolding diff and prompt the user via the interactive dialog:
   - Message: `chore(scaffold): initialize project architecture and agent tracking state`
   - Question: `"Souhaitez-vous enregistrer le commit d'initialisation du projet en local ?"`

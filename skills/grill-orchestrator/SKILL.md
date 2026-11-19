---
name: grill-orchestrator
description: >-
  Use this skill automatically to orchestrate an adversarial "Grill" session for critical tasks: foundational architecture, core 'no_std' implementations, complex protocols, or high-risk code. Do NOT use this skill for trivial tasks (typos, simple refactoring, docs).
---

# Grill Orchestrator Skill

This skill turns the primary agent into an Orchestrator that delegates work to two adversarial subagents: a Maker (who builds) and an Inquisitor (who reviews). 
**CRITICAL:** This process is governed by strict Human-in-the-Loop (HITL) security gates. You must NEVER bypass user authorization at the defined checkpoints.

## 1. Automatic Task Filtering (Proceed vs Abort)
- **PROCEED** if the task involves: foundational architecture, critical `no_std` constraints, complex protocols, or high-risk decisions.
- **ABORT** this skill and handle the task yourself if the task involves: trivial bug fixes, refactoring, documentation, or basic repository setups.

## 2. Model & Workspace Strategy
- **Workspace:** Use `Workspace: "inherit"` so all agents share the same codebase context.
- **Cross-Family Confrontation (Mandatory):** To eliminate cognitive blind spots and shared model biases, the Maker and the Inquisitor should belong to distinct model architectures/families:
  - If Main Session is Claude/Argon: Maker = `pro` or `flash` (Gemini), Inquisitor = `inherit` (Claude/Argon).
  - If Main Session is Gemini: Maker = Local LM Studio (Qwen 3.6 35B via Tier 1) or `flash`, Inquisitor = `pro` (Gemini Pro).
  - The Inquisitor MUST ALWAYS run on a High-reasoning tier (`pro` or `inherit`).

## 3. Workflow Instructions & Security Gates

### Step 1: Initialization
Use `view_file` to read the project's specific constraints (`AGENTS.md`, `docs/agent/RULES.md`, `docs/agent/SESSION_PROTOCOL.md`).

### Step 2: Architecture Draft & Gate 1 (Plan Validation)
1. Spawn the **Maker**. Prompt: "You are the Maker. Draft the architectural solution and present it as a markdown Artifact (set RequestFeedback: true). Do NOT write code yet. Read the project rules first."
2. **SECURITY GATE 1:** Wait for the USER to approve the artifact. If the user requests changes, send them back to the Maker. Do not proceed to coding until the user explicitly approves the plan via the Artifact interface.

### Step 3: Red-Team Test Generation & Implementation Loop
1. **Black-Box Adversarial Tests (Inquisitor as Test Author):**
   - Provide the Inquisitor with ONLY the public contract, types, and specification approved in Gate 1 (black-box).
   - Inquisitor writes dedicated adversarial / stress tests (e.g. `tests/adversarial_*.rs`, `tests/redteam_*.ts`) testing edge cases, boundary values, saturation, malformed inputs, and panic/recovery paths.
   - The Inquisitor is strictly FORBIDDEN from writing or modifying production code, and cannot relax tests to make them pass.
2. **Maker Implementation:**
   - Maker implements the production code and baseline unit tests.
   - The Maker MUST make both baseline tests AND the Inquisitor's adversarial test suite pass.
3. **Adversarial Critique:**
   - Forward the Maker's code diff to the Inquisitor via `send_message`.
   - Inquisitor reviews for subtle flaws, rule violations (`no_std`, hardware coupling), non-idiomatic code smells (lazy `.clone()`, unwrap, manual indexing), and concurrency hazards.
   - If flaws are found, critique is returned to the Maker to fix.
4. **SECURITY GATE 2 (Arbitration):** If the Maker and Inquisitor debate for more than 2 iterations without an "APPROVED" from the Inquisitor, you MUST use the `ask_question` tool to ask the USER to arbitrate. 
   **CRITICAL PRE-REQUISITE:** Before asking the question, you MUST display a summary in the chat containing:
   - The code `diff` or specific snippets causing the dispute.
   - A clear explanation of what the Maker implemented.
   - A clear explanation of the Inquisitor's rejection motive.
   Options must include: 
   - "Give reason to the Inquisitor (Force Maker to rewrite)"
   - "Give reason to the Maker (Override Inquisitor)"

### Step 4: CI & Automated Mutation Testing (cargo-mutants)
Once the Inquisitor approves (or user overrides in Gate 2), run the local verification suite:
1. Standard checks: `cargo fmt --check`, `cargo clippy`, `cargo test` (or `npm test` / `tsc`).
2. **Mutation Testing Oracle (`cargo-mutants` for Rust codebases):**
   - Run `cargo mutants --in-diff` (or on modified modules, e.g. `cargo mutants -F <file>`).
   - If any mutant **survives** ("MISSED"), it proves a blind spot in test coverage. Send the surviving mutant diagnostic to the Inquisitor/Maker to write a killing test case. All mutants in modified code must be killed or explicitly justified.

### Step 5: Final Post-Implementation Grill & Merciless Local Audit (MANDATORY)
Even after initial approval and passing CI, you MUST ALWAYS perform a final adversarial "Grill" pass before requesting commit authorization:
1. **Merciless Local Audit (0 Cloud Tokens)**: Invoke the local model via `tiered-router:execute_local_task` (or the `merciless-local-audit` skill) with the full `git diff`. The local auditor mercilessly scrutinizes the code for:
   - Non-idiomatic anti-patterns, silent allocations, or unwarranted bypasses (`#[allow(...)]`, `@ts-ignore`).
   - Subtle logic inversions (e.g. mute vs volume, polarity, inverted sensor distance ranges).
   - Concurrency & Queue saturation: dropping critical events (like pointer release or state changes) under heavy load or high-frequency loops.
   - Silent error suppression (`let _ = ...`), unhandled variants, or resource leaks.
   - Any `[BLOCKING]` defect reported by the local auditor must be corrected before proceeding.
2. **Mandatory Test Coverage**: Add targeted unit/integration tests explicitly exercising any edge cases or failure modes identified during this final grill.
3. Re-run CI validation (`cargo fmt --check`, `cargo clippy`, `cargo test`) to ensure everything is strictly green with zero warnings.

### Step 6: SECURITY GATE 3 (Commit Validation)
Once all tests pass and the final grill is complete, you are strictly FORBIDDEN from committing automatically.
**CRITICAL PRE-REQUISITE:** Before triggering the `ask_question` tool, you MUST:
1. Run `git diff` (or `git diff --cached` if files are already staged).
2. Present the exact `diff` to the user in a markdown code block.
3. Provide a clear, human-readable summary of exactly what changes were made.
You MUST then use the `ask_question` tool: "The Inquisitor approved, final post-implementation grill is complete, and CI is green. Do you authorize the git commit?"
Options MUST include: 
- "Yes, proceed with commit."
- "No, let me review manually."

### Step 7: Strict State Updates & Handoff
1. If the user authorizes the commit, execute `git commit`.
2. **Mandatory:** Update `docs/agent/SESSION_STATE.md` and `docs/agent/TASKS.md`.
3. **Mandatory:** Write `docs/agent/HANDOFF.md` containing the next logical action.
4. Emit the `SESSION_HANDOFF_REQUIRED` signal in your final message to the user.
5. **Phase Boundary Decision (Context Management):** Remind the user to run `/compact` to purge conversational overhead for the next phase, or `/clear` if switching to an unrelated feature branch.

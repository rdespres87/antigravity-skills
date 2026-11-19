# antigravity-skills

A collection of skills for Google Antigravity and AI coding agents.

## Included Skills

- **`grill-orchestrator`**: Multi-agent adversarial orchestration (Maker vs Inquisitor) with Human-in-the-Loop review gates, black-box red-team test generation, mutation testing oracle (`cargo-mutants`), and mandatory post-implementation audit.
- **`merciless-local-audit`**: Zero-cloud-token adversarial code audit executed by the local offline model (LM Studio via `execute_local_task`) after **every** feature development, refactoring, or bug fix across all projects. Scrutinizes code according to the strict 7-axis rubric:
  1. *Modular Architecture & Anti-God Files* (flags files >400–500 LOC or functions >50 LOC for modular decomposition).
  2. *Algorithmic Performance & Memory Pre-allocation* (avoids unintended $O(N^2)$ loops, excessive clones, and un-sized `Vec::new` allocations).
  3. *Exhaustive Test Coverage* (verifies boundary values, error branches, and edge cases).
  4. *Language Idiomaticity & Craft* (Rust: 0 lazy clone, 0 unwrap in production, typestate/newtype; TS/JS: 0 `any`; Python: PEP 484/585 type hints).
  5. *Logic & Boundary Conditions* (off-by-one, inverted booleans, empty collections, integer overflow/underflow, NaN handling).
  6. *Error Resilience* (no swallowed errors, clear domain variants, strict `?` propagation).
  7. *Concurrency & State Hazards* (race conditions, deadlocks, queue saturations, dropped events).
  8. *Mandatory README & Documentation Sync* (strictly blocking: immediately rejects any commit introducing new features, APIs, or rules without accompanying `README.md` updates).
- **`project-scaffold`**: Automatically scaffolds and initializes new project workspaces with the mandatory Antigravity persistent state architecture (root `AGENTS.md`, `docs/agent/{SESSION_STATE,TASKS,HANDOFF}.md`, and `.gitignore`) before any domain code or business logic is authored.

## Installation

Symlink or copy individual skills into your global Antigravity skills directory:

```bash
# Example: symlink into global skills
ln -s "$(pwd)/skills/merciless-local-audit" ~/.gemini/config/skills/merciless-local-audit
ln -s "$(pwd)/skills/grill-orchestrator" ~/.gemini/config/skills/grill-orchestrator
ln -s "$(pwd)/skills/project-scaffold" ~/.gemini/config/skills/project-scaffold
```

## License

MIT

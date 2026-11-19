# antigravity-skills

A collection of skills for Google Antigravity and AI coding agents.

## Included Skills

- **`grill-orchestrator`**: Multi-agent adversarial orchestration (Maker vs Inquisitor) with Human-in-the-Loop review gates, black-box red-team test generation, mutation testing oracle (`cargo-mutants`), and mandatory post-implementation audit.
- **`merciless-local-audit`**: Zero-cloud-token adversarial code audit executed by the local offline model (LM Studio via `execute_local_task`) after **every** feature development, refactoring, or bug fix across all projects. Scrutinizes language idiomaticity, silent allocations, race conditions, unhandled errors, and missing test coverage.

## Installation

Symlink or copy individual skills into your global Antigravity skills directory:

```bash
# Example: symlink into global skills
ln -s "$(pwd)/skills/merciless-local-audit" ~/.gemini/config/skills/merciless-local-audit
ln -s "$(pwd)/skills/grill-orchestrator" ~/.gemini/config/skills/grill-orchestrator
```

## License

MIT

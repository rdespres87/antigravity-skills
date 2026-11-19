---
name: merciless-local-audit
description: >-
  Mandatory adversarial code audit using the local offline model (LM Studio via execute_local_task) after every feature development or bug fix across any project. Enforces zero cloud token consumption, strict idiomatic craft, error checking, race condition detection, and regression prevention.
---

# Merciless Local Code Audit Skill (Zero Cloud Tokens)

This skill enforces a **mandatory, adversarial, zero-compromise post-implementation review** performed by the local offline LLM (via LM Studio / `tiered-router:execute_local_task`) after **EVERY** code modification, new feature implementation, refactoring, or bug fix.

It consumes **0 cloud tokens** and serves as an uncompromising quality gate before any code is committed, reviewed by human, or marked as done.

---

## 1. When to Trigger (Mandatory Triggers)

Execute this audit:
- **Immediately after any code implementation** (new feature, function, component).
- **Immediately after any bug fix or regression repair**.
- **Before running Gate 3 / Commit validation** or marking a task as completed.
- **Before handing off** or emitting `SESSION_HANDOFF_REQUIRED`.

Do NOT bypass this step. It catches subtle edge cases, silent regressions, memory allocations, and non-idiomatic patterns before they reach the main repository history.

---

## 2. Execution Protocol

### Step 1: Collect the Diff or Modified Code
Obtain the unified diff of the changes:
```bash
git diff HEAD
# or if files are already staged:
git diff --cached
# or if working across commits:
git diff HEAD~1 HEAD
```
If the project is not in a git repo or changes are isolated, collect the modified file snippets.

**Mandatory Headroom Compression (>80 lines)**:
If the diff exceeds 80 lines or 800 tokens, you MUST compress it first via Headroom:
```python
call_mcp_tool(
    ServerName="headroom",
    ToolName="headroom_compress",
    Arguments={"content": diff_content}
)
```
Pass the compressed output to Step 2 to preserve context tokens, while keeping the hash for retrieval if deep slicing is required.

### Step 2: Invoke the Local Model via `execute_local_task`
Call the MCP tool `execute_local_task` on `tiered-router` with an aggressive inquisitorial prompt:

```python
call_mcp_tool(
    ServerName="tiered-router",
    ToolName="execute_local_task",
    Arguments={
        "system_prompt": (
            "You are a merciless, ultra-strict principal software architect, security auditor, and language purist. "
            "Your role is an adversarial code reviewer with ZERO leniency. "
            "Examine the provided code diff and identify every single vulnerability, logic error, regression risk, "
            "code smell, non-idiomatic pattern, silent failure, and missing test case. "
            "Do not compliment. Do not be polite. Categorize findings into [BLOCKING] and [ADVISORY]."
        ),
        "prompt": (
            "Perform a merciless code audit on the changes below.\n\n"
            "Strict Checklist:\n"
            "1. Language Idiomaticity & Craft:\n"
            "   - Rust: Zero lazy `.clone()`, zero unwrap/expect in production code, iterators over C-style index loops, newtype/typestate patterns, zero-allocation in real-time/DSP.\n"
            "   - TypeScript/JS: Strict types (zero `any`), discriminated unions, proper async/await, immutable transforms.\n"
            "   - Python: Strict type hints, context managers, frozen dataclasses, no bare exceptions.\n"
            "2. Logic & Edge Cases: Off-by-one errors, boundary conditions, inverted booleans/comparisons, empty collection handling, integer overflow/underflow.\n"
            "3. Error Handling & Resilience: No swallowed errors (`let _ = ...`, bare catch), proper error propagation (`?`), clear error variants.\n"
            "4. Concurrency & State: Race conditions, deadlock risks, queue saturation, dropped events, stale state.\n"
            "5. Test Coverage: Are there unverified execution branches, boundary cases, or failure modes lacking unit tests?\n\n"
            "Format your output as:\n"
            "### [BLOCKING DEFECTS] (Must be resolved before commit)\n"
            "- <file>:<line>: <Exact flaw and concrete fix>\n\n"
            "### [ADVISORY / POLISH]\n"
            "- <file>:<line>: <Suggestion>\n\n"
            "### VERDICT: [REJECTED (flaws found) | APPROVED (clean)]"
        ),
        "context": "<DIFF_CONTENT>",
        "max_tokens": 3072,
        "temperature": 0.1,
        "fast": False
    }
)
```

### Step 3: Act on the Audit Verdict

1. **If Verdict is `REJECTED` (Blocking Defects Found):**
   - **Do NOT commit or mark task done.**
   - Apply fixes to resolve all `[BLOCKING]` defects.
   - For isolated mechanical fixes, feed the flaw back into `execute_local_task` (Auto-Healing Loop) to fix locally at 0 cloud tokens.
   - Run native compiler/linter/tests (`cargo check`, `cargo test`, `tsc --noEmit`, etc.) to confirm compilation.
   - Re-run the local audit on the updated diff until verdict is `APPROVED`.

2. **If Verdict is `APPROVED`:**
   - Review any `[ADVISORY]` items. Apply valuable polish if trivial.
   - Proceed to test suite validation and the next workflow stage (commit gate, user feedback).

3. **Fallback Handling (If LM Studio is Unavailable):**
   - If `execute_local_task` reports that LM Studio is offline or no model is loaded:
     - Inform the user: *"LM Studio offline - Falling back to Cloud Inquisitor audit."*
     - Delegate the audit prompt to a `flash` or `pro` subagent so quality is never compromised.

---
name: python-ai-agent-engineering
description: Guide for designing and maintaining Python AI agents with clear module boundaries, safe tool usage, and reliable structured outputs. Use when creating or refactoring Python agent projects.
---

Use this skill to build Python AI agents that are understandable, testable, and safe to operate.

## Objectives

1. Separate orchestration, prompts, tools, and schemas.
2. Keep tool behavior deterministic and validated.
3. Make model I/O explicit and strongly typed.
4. Avoid unsafe execution shortcuts in agent code.

## Standard Package Shape

```text
my_agent/
├── __init__.py
├── agent.py
├── prompts.py
├── tools.py
├── schemas.py
└── runtime.py
```

## Module Responsibilities

### `agent.py` (wiring/orchestration)

- Compose model, tools, and prompt policy.
- Keep this file thin: no long business logic blocks.
- Expose a small entrypoint for invocation.

### `prompts.py` (behavior policy)

- Keep system instructions and reusable templates centralized.
- Use concise, explicit instructions with domain constraints.
- Prefer parameterized templates over duplicated prompt text.

### `tools.py` (capabilities)

- Implement focused tool functions with clear docstrings.
- Validate all inputs before side effects.
- Return predictable, serializable outputs.
- Do not use raw `eval` for expression execution or code generation paths.

### `schemas.py` (contracts)

- Define Pydantic models for tool input/output and model responses.
- Use field descriptions to improve tool-call reliability.
- Make optional fields intentional and documented.

### `runtime.py` (execution helpers)

- Handle environment configuration, retries, and runtime adapters.
- Keep provider-specific setup isolated from core agent logic.

## Safety and Reliability Rules

1. Fail loudly on invalid tool inputs; do not silently coerce critical values.
2. Use allowlisted operations for file/system-facing tools.
3. Keep retries bounded and observable.
4. Propagate meaningful errors back to callers.
5. Add tests for schema validation, tool edge cases, and agent flow.

## Refactor Workflow

When cleaning up an existing Python agent:

1. Move prompt strings out of orchestration code into `prompts.py`.
2. Split large monolithic agent files into module responsibilities above.
3. Add/strengthen Pydantic schemas for every external boundary.
4. Remove unsafe helpers and replace with explicit validated logic.
5. Update tests to preserve behavior while improving structure.

## Quality Bar

- Clear boundaries between orchestration and implementation details.
- Explicit, typed interfaces across modules.
- No unsafe dynamic execution patterns.
- Deterministic tool behavior and useful error messages.

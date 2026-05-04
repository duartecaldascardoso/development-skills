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

## Example: Minimal, Safe Agent Module Split

`agent.py`

```python
from langchain.agents import create_agent
from .prompts import SYSTEM_PROMPT
from .tools import search_codebase

agent = create_agent(
    model="anthropic:claude-sonnet-4-5",
    tools=[search_codebase],
    system_prompt=SYSTEM_PROMPT,
)

def run_agent(user_input: str):
    return agent.invoke({"messages": [{"role": "user", "content": user_input}]})
```

`prompts.py`

```python
SYSTEM_PROMPT = """
You are a senior software engineer assistant.
Prefer explicit, verifiable answers.
When using tools, explain assumptions and limits.
""".strip()
```

`schemas.py`

```python
from pydantic import BaseModel, Field

class CodeSearchInput(BaseModel):
    query: str = Field(min_length=1, description="Text or regex to search for.")
    path: str = Field(default=".", description="Directory to search within.")

class CodeSearchResult(BaseModel):
    matches: list[str]
```

`tools.py`

```python
from pathlib import Path
from .schemas import CodeSearchInput, CodeSearchResult

def search_codebase(query: str, path: str = ".") -> dict:
    args = CodeSearchInput(query=query, path=path)
    base = Path(args.path).resolve()
    if not base.exists() or not base.is_dir():
        raise ValueError(f"Invalid search path: {base}")

    matches: list[str] = []
    for file in base.rglob("*.py"):
        text = file.read_text(encoding="utf-8")
        if args.query in text:
            matches.append(str(file))

    return CodeSearchResult(matches=matches).model_dump()
```

`runtime.py`

```python
import os

def require_env(name: str) -> str:
    value = os.getenv(name)
    if not value:
        raise RuntimeError(f"Missing required environment variable: {name}")
    return value
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

#### Bad vs Good Tool Safety

```python
# Bad
def calculate(expr: str) -> str:
    return str(eval(expr))
```

```python
# Good
import ast
import operator as op

OPS = {ast.Add: op.add, ast.Sub: op.sub, ast.Mult: op.mul, ast.Div: op.truediv}

def safe_calculate(expr: str) -> float:
    node = ast.parse(expr, mode="eval").body
    if not isinstance(node, ast.BinOp) or type(node.op) not in OPS:
        raise ValueError("Only simple binary arithmetic is allowed.")
    if not isinstance(node.left, ast.Constant) or not isinstance(node.right, ast.Constant):
        raise ValueError("Only numeric constants are allowed.")
    return OPS[type(node.op)](float(node.left.value), float(node.right.value))
```

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

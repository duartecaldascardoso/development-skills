---
name: python-ai-agent-engineering
description: Guide for designing and maintaining Python AI agents with clear module boundaries, safe tool usage, and reliable structured outputs. Use when creating or refactoring Python agent projects.
---

Use this skill to build Python AI agents that are understandable, testable, and safe to operate.
This skills relies heavily on the abstraction provided by LangChain for create_agent, but the principles apply broadly to any Python agent architecture.

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
└── schemas.py # Optional but recommended for complex agents with Pydantic models
```

## Example: Minimal, Safe Agent Module Split

`agent.py`

```python
from langchain.agents import create_agent
from .prompts import SYSTEM_PROMPT
from .tools import search_codebase

# Relying on the create_agent abstraction to handle orchestration, but we could implement our own loop if we wanted more control.
agent = create_agent(
    model="anthropic:claude-sonnet-4-5",
    tools=[search_codebase],
    system_prompt=SYSTEM_PROMPT,
    # Much more configuration can go here, such as Middleware, retries, config, etc.
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
from langchain.tools import tool

@tool
def search_database(query: str, limit: int = 10) -> str:
    """Search the customer database for records matching the query.

    Args:
        query: Search terms to look for
        limit: Maximum number of results to return
    """
    return f"Found {limit} results for '{query}'"
```

## Module Responsibilities

### `agent.py` (wiring/orchestration)

- Compose model, tools, and prompt policy.
- Keep this file thin: no long business logic blocks.
- Optionally, expose a small 'main' entrypoint for invocation and testing.

### `prompts.py` (behavior policy)

- Keep system instructions and reusable templates centralized.
- Use concise, explicit instructions with domain constraints.
- Prefer parameterized templates over duplicated prompt text.
- Use markdown formatting for readability and structure.

### `tools.py` (capabilities)

- Implement focused tool functions with clear docstrings.
- Validate all inputs before side effects.
- Return predictable, serializable outputs.

### `schemas.py` (contracts)

- Define Pydantic models for tool input/output and model responses.
- Use field descriptions to improve tool-call reliability.
- Make optional fields intentional and documented.

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

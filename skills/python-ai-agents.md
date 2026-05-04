# Creating and Maintaining Python AI Agents

This document outlines the approach for building, structuring, and maintaining Artificial Intelligence agents in Python, focusing on the **LangChain** ecosystem.

## Core Philosophy

Building AI agents requires managing state, handling tool execution, and recovering from errors. To maintain clean, scalable, and testable agentic applications, you should strictly organize your codebase by separating concerns. A single "Agent" should not be a monolithic Python file, but a cohesive package of specialized modules.

### Always Stay Updated

Always refer to the official LangChain documentation for the latest patterns:
*   [LangChain Agents Reference](https://reference.langchain.com/python/langchain/agents)

## Recommended Project Structure

A robust LangChain agent should be structured into separate files within its own directory or package to maintain modularity:

```
my_agent/
├── __init__.py
├── agent.py        # Main agent definition and orchestration
├── prompts.py      # System prompts, templates, and instructions
├── tools.py        # Custom tools and functions the agent can call
└── schemas.py      # Pydantic models for structured output and data validation
```

### 1. The Core Agent Definition (`agent.py`)

This file contains the orchestration logic. This file wires together the LLM and the tools.

*   **Do:** Define the agent using `create_agent` and execute it.
*   **Don't:** Hardcode long prompts or complex tool logic here.

```python
# my_agent/agent.py
from langchain.agents import create_agent
from .tools import check_weather
from .prompts import SYSTEM_PROMPT

graph = create_agent(
    model="anthropic:claude-sonnet-4-5-20250929",
    tools=[check_weather],
    system_prompt=SYSTEM_PROMPT,
)

inputs = {"messages": [{"role": "user", "content": "what is the weather in sf"}]}
for chunk in graph.stream(inputs, stream_mode="updates"):
    print(chunk)
```

### 2. Prompts and Templates (`prompts.py`)

Keep all instructions, personas, and few-shot examples centralized here. This makes tweaking the agent's behavior much easier without digging through application logic.

```python
# my_agent/prompts.py
SYSTEM_PROMPT = "You are a helpful assistant"
```

### 3. Tools (`tools.py`)

Agents interact with the world via tools. Define your functions here. Ensure each tool has a clear docstring, as LangChain uses this docstring to tell the LLM when and how to use the tool.

```python
# my_agent/tools.py
@.antigravity/extensions/james-yu.latex-workshop-10.13.1-universal/data/packages/datatool-base.json
def check_weather(location: str) -> str:
    '''Return the weather forecast for the specified location.'''
    return f"It's always sunny in {location}"
```

### 4. Schemas and Data Models (`schemas.py`)

Use `Pydantic` to define the input arguments for your tools and the expected structured output from the LLM.

```python
# my_agent/schemas.py
from pydantic import BaseModel, Field

class CodeSearchInput(BaseModel):
    query: str = Field(description="The exact text or regex to search for in the code.")
    dir_path: str = Field(default=".", description="The directory path to restrict the search.")
```
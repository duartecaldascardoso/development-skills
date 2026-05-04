# Creating and Maintaining Python AI Agents

This document outlines the best practices for building, structuring, and maintaining Artificial Intelligence agents in Python, heavily leaning on the **LangChain** ecosystem and its underlying orchestration framework, **LangGraph**.

## Core Philosophy

Building AI agents is more complex than simple LLM calls. It requires managing state, handling tool execution, recovering from errors, and ensuring observability. 

To maintain clean, scalable, and testable agentic applications, you should strictly organize your codebase by separating concerns. A single "Agent" should not be a monolithic Python file, but a cohesive package of specialized modules.

### Always Stay Updated

The field of AI agents is evolving rapidly. Always refer to the official LangChain documentation for the latest patterns, particularly:
*   [LangChain Agents Reference](https://reference.langchain.com/python/langchain/agents)
*   [LangGraph Documentation](https://python.langchain.com/docs/langgraph)

## Recommended Project Structure

A robust LangChain agent should be structured into separate files within its own directory or package to maintain modularity:

```
my_agent/
├── __init__.py
├── agent.py        # Main graph/agent definition and orchestration
├── prompts.py      # System prompts, templates, and instructions
├── tools.py        # Custom tools and functions the agent can call
├── schemas.py      # Pydantic models for structured output and data validation
└── state.py        # TypedDict or Pydantic models defining the agent's state (LangGraph)
```

### 1. The Core Agent Definition (`agent.py`)

This file contains the orchestration logic. In modern LangChain, this typically involves defining a **LangGraph**. This file wires together the LLM, the tools, and the state transitions.

*   **Do:** Define nodes, edges, conditional routing, and compile the graph.
*   **Don't:** Hardcode long prompts or complex tool logic here.

```python
# my_agent/agent.py
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI
from .state import AgentState
from .prompts import SYSTEM_PROMPT
from .tools import my_tools

# Example LangGraph setup
llm = ChatOpenAI(model="gpt-4o")
llm_with_tools = llm.bind_tools(my_tools)

def call_model(state: AgentState):
    messages = state['messages']
    # Prepend system prompt if needed
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

# ... Define graph, nodes, and edges ...
workflow = StateGraph(AgentState)
workflow.add_node("agent", call_model)
# ...
```

### 2. Prompts and Templates (`prompts.py`)

LLMs are highly sensitive to prompts. Keep all instructions, personas, and few-shot examples centralized here. This makes tweaking the agent's behavior much easier without digging through application logic.

```python
# my_agent/prompts.py
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

SYSTEM_PROMPT = """You are an expert software engineer assistant.
Your goal is to help users write and debug code.
Always use the provided tools to search the codebase before answering.
"""

AGENT_PROMPT_TEMPLATE = ChatPromptTemplate.from_messages([
    ("system", SYSTEM_PROMPT),
    MessagesPlaceholder(variable_name="messages"),
])
```

### 3. Tools (`tools.py`)

Agents interact with the world via tools. Define your `@tool` decorated functions here. Ensure each tool has a clear docstring, as LangChain uses this docstring to tell the LLM when and how to use the tool.

```python
# my_agent/tools.py
from langchain_core.tools import tool
from .schemas import CodeSearchInput

@tool(args_schema=CodeSearchInput)
def search_codebase(query: str, dir_path: str = "."):
    """
    Searches the local codebase for a given query.
    Use this tool when you need to find specific function definitions or usage examples.
    """
    # ... Implementation logic ...
    return f"Found results for {query} in {dir_path}"

my_tools = [search_codebase]
```

### 4. Schemas and Data Models (`schemas.py`)

Use `Pydantic` to define the input arguments for your tools and the expected structured output from the LLM. This provides type safety, automatic validation, and clear schemas for the LLM to follow.

```python
# my_agent/schemas.py
from pydantic import BaseModel, Field

class CodeSearchInput(BaseModel):
    query: str = Field(description="The exact text or regex to search for in the code.")
    dir_path: str = Field(default=".", description="The directory path to restrict the search.")

class AgentFinalResponse(BaseModel):
    explanation: str = Field(description="A detailed explanation of the solution.")
    code_snippet: str = Field(description="The final code snippet to provide to the user.")
```

### 5. State Definition (`state.py`)

When using LangGraph (the standard for complex LangChain agents), you need to define the state that is passed between nodes.

```python
# my_agent/state.py
from typing import TypedDict, Annotated, Sequence
import operator
from langchain_core.messages import BaseMessage

class AgentState(TypedDict):
    # The `operator.add` reducer appends new messages instead of overwriting
    messages: Annotated[Sequence[BaseMessage], operator.add]
    # Add other state variables as needed
    current_directory: str
    error_count: int
```

## Best Practices & Modern Features

*   **Use LangGraph:** For anything beyond a simple conversational chain, use LangGraph. It provides durable execution, human-in-the-loop capabilities, and precise control over the agent's control flow.
*   **Leverage Middleware:** LangChain offers middleware for execution limits, PII redaction, context window management, and automatic retries. Use these to make your agent robust.
*   **Observability with LangSmith:** Always integrate LangSmith (or a similar tool) during development and production. Tracing agent execution steps, tool inputs/outputs, and latency is critical for debugging non-deterministic AI behavior.
*   **Manage Context Size:** Agents can quickly consume their context window. Implement mechanisms (or use LangChain middleware) to summarize old messages or trim the context history when it grows too large.
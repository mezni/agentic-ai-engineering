# Agentic AI Engineering

A small, dependency-light customer support agent written in Python. It demonstrates the core agentic loop: the LLM decides what to do, requests tools, receives observations, and repeats until it can answer the user.

The project is intentionally minimal so the mechanics stay visible: one controller, one LLM client, a typed tool executor, and mock support data.

## Features

- **Bounded agent loop** — the controller runs at most `max_steps` iterations, preventing runaway execution.
- **Tool calling** — the model can request `get_order` and `get_shipping_status`; tool schemas are declared as strict JSON Schema.
- **Validated tool inputs** — every tool call is parsed with Pydantic before execution; unknown tools and invalid arguments return structured errors.
- **Explicit agent state** — conversation items, observations, iteration count, and status are tracked in `AgentState`.
- **OpenAI Responses API** — model and API key are configured through environment variables.
- **Tests and linting** — unit tests cover tools and the executor; Ruff enforces style.

## Architecture

```text
main.py
   |
   v
AgentController ──────> LLMClient ──────> OpenAI Responses API
   |                        ^
   |                        |
   └──> ToolExecutor ───> support_tools (mock ORDERS / SHIPPING data)
```

The loop works as follows:

1. The user message is added to the conversation.
2. The LLM receives the conversation plus the available tool definitions.
3. If the model returns no tool call, its text becomes the final response.
4. If it returns tool calls, each is validated and executed, and the result is appended to the conversation as a `function_call_output`.
5. Steps 2–4 repeat until a final response is produced or `max_steps` is reached.

## Project structure

```text
.
├── src/
│   ├── agentic_ai/
│   │   ├── agent.py              # AgentController: bounded agent loop
│   │   ├── llm_client.py         # OpenAI client and system instructions
│   │   ├── main.py               # Demo entry point
│   │   ├── state.py              # AgentState dataclass
│   │   └── tools/
│   │       ├── executor.py       # Validates and executes tool calls
│   │       ├── registry.py       # JSON Schema tool definitions
│   │       └── support_tools.py  # Mock order and shipping data
│   └── agentic_ai_engineering/   # Package scaffold and script entry point
├── tests/
│   ├── test_executor.py
│   └── test_tools.py
├── pyproject.toml
└── .env.example
```

## Requirements

- Python 3.12+
- [uv](https://docs.astral.sh/uv/)
- An OpenAI API key

## Setup

Install dependencies:

```bash
uv sync
```

Create a local environment file:

```bash
cp .env.example .env
```

Then set your credentials:

```dotenv
OPENAI_API_KEY=your_api_key
OPENAI_MODEL=gpt-6-astra
```

| Variable         | Required | Default       | Description                          |
| ---------------- | -------- | ------------- | ------------------------------------ |
| `OPENAI_API_KEY` | Yes      | —             | API key used by the OpenAI client.   |
| `OPENAI_MODEL`   | No       | `gpt-6-astra` | Model used for agent decisions.      |

## Running the demo

```bash
uv run python -m agentic_ai.main
```

The demo asks the agent to investigate order `#4821`, prints each iteration and tool result, then prints the final response.

## Tools

| Tool                  | Arguments  | Description                                  |
| --------------------- | ---------- | -------------------------------------------- |
| `get_order`           | `order_id` | Returns order status and customer ID.        |
| `get_shipping_status` | `order_id` | Returns shipping status and estimated date.  |

Data is mocked in `src/agentic_ai/tools/support_tools.py`. Only order `4821` exists; other IDs return a structured `not found` error.

## Development

Run the tests:

```bash
uv run pytest
```

Run the linter:

```bash
uv run ruff check .
```

## Limitations

- Mock, in-memory data only — there is no persistence or external service integration.
- A single hardcoded demo scenario in `main.py`.
- No authentication, authorization, guardrails, or human approval flow yet.
- Tool results are trusted as-is; no output validation is performed.

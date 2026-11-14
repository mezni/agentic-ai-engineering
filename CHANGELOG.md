# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned

- Replace mock support data with repository adapters.
- Add guardrails, role-based access, and a human approval flow.
- Add observability and evaluation coverage.
- Expose the agent through a service or CLI.

## [0.1.0] - 2026-11-14

### Added

- Project foundation with `uv` dependency management, Python 3.12, pytest, and Ruff.
- `AgentState` dataclass tracking conversation items, observations, iterations, status, and final response.
- `LLMClient` backed by the OpenAI Responses API, with model selection via `OPENAI_MODEL` and support-specific system instructions.
- `AgentController` implementing a bounded agent loop with a configurable `max_steps` limit.
- Tool registry with strict JSON Schema definitions for `get_order` and `get_shipping_status`.
- `ToolExecutor` with Pydantic argument validation and structured errors for unknown tools and failed executions.
- Mock order and shipping data for the customer support scenario.
- Demo entry point that investigates order `#4821`.
- Unit tests covering the support tools and the tool executor.
- README with setup, usage, architecture, and project structure documentation.

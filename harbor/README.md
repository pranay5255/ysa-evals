# Harbor Eval Framework

## Installation

```bash
# via uv (recommended)
uv tool install harbor

# via pip
pip install harbor
```

## Quick Start

```bash
# View all commands
harbor --help

# List registered datasets
harbor dataset list

# Run an eval with a registered dataset
harbor run -d "<org/name>" -m "<model>" -a "<agent>"

# Run a local dataset
harbor run -p "<path/to/dataset>" -m "<model>" -a "<agent>"

# Run with cloud sandbox (Daytona) — 32 parallel trials
harbor run -d "<org/name>" -m "<model>" -a "<agent>" --env daytona -n 32
```

## Available Datasets (from Harbor Registry)

- `MichaelY310/devopsgym` — 728 tasks: Build, Monitoring, Issue Resolving, Test Gen, E2E
- `pgcodellm/rebench-v2-test` — 87 tasks
- `benchflow/skillsbench` — 205 tasks
- `factory-ai/legacy-bench` — 10 tasks
- `scale-ai/swe-atlas-qna` — 124 tasks (codebase QnA)
- `scale-ai/swe-atlas-tw` — 90 tasks (test writing)
- `cais/swebenchpro` — 731 tasks (Python/JS/TS/Go, git-isolated)
- `LiteCoder/LiteCoder-rl` — 602 tasks
- `bixbench` — 205 tasks
- `bigcode/humanevalfix` — 164 tasks
- `crustbench/crustbench` — 100 tasks
- And 80+ more

## Terminus-2 Agent

Terminus-2 is Harbor's flagship agent, implemented in `harbor.agents.terminus_2`:

```python
from harbor.agents.terminus_2 import Terminus2

agent = Terminus2(
    logs_dir=Path("./logs"),
    model_name="gpt-4-turbo",
    max_turns=100,
    parser_name="json",  # or "xml"
    temperature=0.7,
    record_terminal_session=True,
)
```

### Key Features
- **Tmux-based stateful shell** (unlike mini-swe-agent's stateless subprocess.run)
- **asciinema terminal recording**
- **Proactive context summarization** with subagents
- **JSON and XML parsers** for LLM response format
- **Interleaved thinking** support
- LiteLLM backend for multi-model support

## Key Difference: mini-swe-agent vs Terminus-2

| Feature | mini-swe-agent | Terminus-2 |
|---------|---------------|------------|
| Execution | Stateless subprocess.run | Stateful tmux session |
| History | Linear (trajectory = messages) | Chat history with summarization |
| Tools | Bash only | Full toolkit + tmux |
| Terminal recording | ❌ | ✅ asciinema |
| Eval framework | SWE-bench | **Harbor** |
| Sandbox | Docker/Podman/Singularity | Docker + Harbor |

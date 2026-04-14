# Agent Comparison Analysis — mini-swe-agent vs Alternatives

## Executive Summary

mini-swe-agent is a **100-line minimal bash-only agent** that scores >74% on SWE-bench verified. This analysis compares it against:
1. **Terminus-2** (Harbor eval framework's flagship agent)
2. **mini-ts-swe-agent** (TypeScript port)
3. **OpenCode** (Go-based CLI agent)
4. **Aider** (Python pair-programming agent)
5. **Plandex** (Go-based long-task agent)

---

## Architecture Comparison

### mini-swe-agent (SWE-agent/mini-swe-agent)

```
Agent.run(task)
  └── messages = [system_prompt, user(task)]
      while True:
          step()
              ├── query()  → LLM API call
              └── execute_actions()  → subprocess.run(cmd) [stateless]
                  └── append observation to messages
              if exit in last msg: break
```

**Key design decisions:**
- No tools other than bash — LM uses shell as its entire interface
- Stateless execution: every `subprocess.run` spawns fresh process
- Linear message history: trajectory == messages sent to LLM
- Swap `subprocess.run` → `docker exec` for sandboxing

### Terminus-2 (Harbor Framework)

```
Terminus2(...)
  ├── _init_llm(llm_backend, model_name, ...)
  ├── setup(environment)
  │   └── TmuxSession(...)
  └── run(task)
      ├── _chat = Chat(...)
      ├── while episodes < max_episodes:
      │   ├── _chat.send_message(prompt)
      │   ├── parse response (json or xml parser)
      │   ├── execute keystrokes via tmux
      │   ├── record terminal (asciinema)
      │   └── handle summarization subagents
      └── return trajectory
```

**Key design decisions:**
- Full tmux session (stateful shell) — NOT stateless
- Terminal recording via asciinema
- Proactive context summarization with subagents
- Supports interleaved thinking
- Parser-based response format (JSON or XML)

### mini-ts-swe-agent (TypeScript Port)

```
LangGraphAgent(...)
  ├── graph = createAgentGraph(environment, config)
  └── async run(task)
      └── stream = graph.stream(initialState)
          └── for await chunk in stream:
              └── node output → messages, check isComplete
```

**Key design decisions:**
- Uses LangGraph state machine (~400+ lines vs 100 in Python)
- `child_process.exec` for execution (stateless like mini-swe-agent)
- More complex, not simpler — LangGraph adds overhead
- No SWE-bench benchmark data

### OpenCode (Go)

```
OpenCode
  ├── Bubble Tea TUI (charmbracelet/bubbletea)
  ├── SQLite persistence (sessions)
  ├── LSP integration
  └── Tool execution (bash, file ops, editor)
```

**Key design decisions:**
- TUI-based interactive CLI
- Session persistence in SQLite
- NOT designed for automated benchmarking
- No published SWE-bench scores

### Aider (Python)

```
AiderAgent
  ├── Tree-sitter code map
  ├── Git integration
  └── Chat-based LLM interaction with repo context
```

**Key design decisions:**
- Repo-map using Tree-sitter for codebase awareness
- Auto git commits with sensible messages
- Lint/test cycling on file changes
- NOT designed for sandboxed benchmarks
- No published SWE-bench scores

### Plandex (Go)

```
PlandexAgent
  ├── Plan-based task decomposition
  ├── Concurrent file editing
  └── Session-based context
```

**Key design decisions:**
- Plans as first-class abstraction
- Concurrent operations on files
- Designed for long-running real-world tasks
- NOT designed for benchmarks

---

## Detailed Comparison Table

| Property | mini-swe-agent | Terminus-2 | mini-ts | OpenCode | Aider | Plandex |
|----------|---------------|------------|---------|----------|-------|---------|
| **Language** | Python | Python | TypeScript | Go | Python | Go |
| **Stars** | 3.8k | — | 2 | 12k | 43.3k | 15.2k |
| **Minimal bash-only** | ✅ YES | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Lines of agent core** | ~100 | ~500+ | ~400+ | N/A | N/A | N/A |
| **Execution model** | subprocess.run (stateless) | tmux (stateful) | child_process.exec | process | LLM chat | session |
| **Linear history** | ✅ YES | ❌ | ✅ | ❌ | ❌ | ❌ |
| **SWE-bench verified score** | >74% | Unknown | Unknown | None | None | None |
| **Sandbox support** | Docker/Podman/Singularity/Bubblewrap/Contree | Docker + Harbor | Likely | ❌ | ❌ | ❌ |
| **Harbor integration** | ❌ | ✅ Native | ❌ | ❌ | ❌ | ❌ |
| **Terminal recording** | ❌ | ✅ asciinema | ❌ | ❌ | ❌ | ❌ |
| **Tools beyond bash** | ❌ None | ✅ Full toolkit | ❌ None | ✅ TUI, LSP | ✅ git, lint, voice, images | ✅ plan mgmt |
| **Context summarization** | ❌ | ✅ proactive | ❌ | ✅ auto-compact | ❌ | ❌ |
| **Eval framework** | SWE-bench | **Harbor** | None | None | None | None |

---

## Environment Interface (Swap Point)

mini-swe-agent exposes this abstraction:

```python
class Environment(Protocol):
    def execute(self, action: str) -> dict:  # returns {stdout, stderr, exitCode}
    def get_template_vars(self) -> dict
    def serialize(self) -> dict

# Swap examples:
LocalEnvironment()        # subprocess.run locally
DockerEnvironment()       # docker exec in container
SingularityEnvironment()  # singularity exec
```

**Can you swap mini-swe-agent with Terminus-2?** No — Terminus-2 uses:
- `TmuxSession` instead of `subprocess.run`
- `BaseEnvironment` from Harbor's own interface
- No documented swap path

**Can you swap mini-swe-agent with mini-ts-swe-agent?** Likely yes — same stateless exec pattern.

**Most other frameworks have NO abstraction equivalent** — they couple execution directly to their CLI/TUI.

---

## Terminus-2 Architecture (from source)

```python
class Terminus2(BaseAgent):
    SUPPORTS_ATIF: bool = True

    def __init__(
        self,
        logs_dir: Path,
        model_name: str | None = None,
        max_turns: int | None = None,
        parser_name: str = "json",  # json or xml
        api_base: str | None = None,
        temperature: float = 0.7,
        reasoning_effort: str | None = None,
        enable_summarize: bool = True,
        proactive_summarization_threshold: int = 8000,
        record_terminal_session: bool = True,
        interleaved_thinking: bool = False,
        llm_backend: LLMBackend | str = LLMBackend.LITELLM,
        trajectory_config: TrajectoryConfig | None = None,
        tmux_pane_width: int = 160,
        tmux_pane_height: int = 40,
        *args, **kwargs
    ):
```

Uses LiteLLM for model abstraction. TmuxSession for terminal. Supports both JSON and XML parsers for LLM responses.

---

## Harbor Framework Integration

Harbor is designed for agent evals with cloud sandbox support:

```bash
# Install
uv tool install harbor

# Run a registered dataset
harbor run -d "<org/name>" -m "<model>" -a "<agent>"

# Run local dataset
harbor run -p "<path/to/dataset>" -m "<model>" -a "<agent>"

# Run with cloud sandbox (Daytona)
harbor run -d "<org/name>" -m "<model>" -a "<agent>" --env daytona -n 32
```

**Registered datasets** (from Harbor registry):
- `MichaelY310/devopsgym` — 728 tasks across Build/Monitoring/Issue Resolving/Test Gen/E2E
- `pgcodellm/rebench-v2-test` — 87 tasks
- `benchflow/skillsbench` — 205 tasks
- `factory-ai/legacy-bench` — 10 tasks
- `scale-ai/swe-atlas-qna` — 124 tasks
- `cais/swebenchpro` — 731 tasks (Python/JS/TS/Go, git history isolated)
- `LiteCoder/LiteCoder-rl` — 602 tasks
- And 80+ more datasets

**Terminal-Bench datasets on HuggingFace:**
- `DCAgent2/terminal_bench_2_a1_nebius_swe_agent` — 265 rows, 31 tasks
- `DCAgent2/DCAgent2_terminal_bench_2_DCAgent_staqc-sandboxes-traces-terminus-2-*` — Terminus-2 traces
- `mlfoundations-dev/gsm8k-terminal-bench-traces-qwen3-8b` — terminal traces

---

## Conclusions

1. **mini-swe-agent is unique** — it's the only minimal bash-only agent with a >74% SWE-bench score. Its stateless `subprocess.run` pattern makes sandboxing trivial.

2. **Terminus-2 is NOT minimal** — it's a full-featured agent with tmux sessions, terminal recording, summarization subagents, and a parser system. It is more comparable to the full SWE-agent than to mini-swe-agent.

3. **mini-ts-swe-agent is NOT simpler than mini-swe-agent** — the TypeScript port with LangGraph is ~4x larger. It adds structure at the cost of minimalism.

4. **No golang/rust SWE-bench-minimal alternative exists** — OpenCode and Plandex are general coding assistants, not benchmarked evaluators.

5. **The swap point is the Environment abstraction** — mini-swe-agent's documented `Environment.execute(action)` swap path works for sandboxing. Other frameworks don't expose an equivalent interface.

6. **Harbor is the eval framework for Terminus-2** — it supports parallel cloud sandbox execution and has 80+ registered datasets. mini-swe-agent uses SWE-bench directly.

7. **pi/coding agents ecosystem is fragmented** — "pi" shows up across multiple unrelated repos with skills/adapters. agent-deck provides a unified TUI for monitoring multiple agents including Terminus-2, Claude Code, OpenCode, and Codex.

---

## Recommendations

- **For sandboxed SWE-bench evaluation**: Use mini-swe-agent with DockerEnvironment
- **For Harbor-based eval pipeline**: Use Terminus-2 with Harbor framework  
- **For mixing agents**: Only mini-swe-agent ↔ mini-ts-swe-agent are swappable
- **For monitoring**: agent-deck provides unified view across multiple agents

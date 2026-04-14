# ysa-evals — Yudai SWE-Agent Evaluation Suite

> Analysis repo for evaluating coding agents (mini-swe-agent, Terminus-2, and alternatives) using Harbor eval framework and Terminal-Bench datasets.

## What's Here

```
ysa-evals/
├── README.md                  # This file
├── ANALYSIS.md               # Full agent comparison analysis
├── agents/                   # Agent implementations studied
│   ├── mini-swe-agent/       # SWE-agent/mini-swe-agent (Python, ~100 lines)
│   ├── mini-ts-swe-agent/     # TypeScript port with LangGraph
│   ├── terminus-2/           # Harbor's Terminus-2 agent
│   ├── opencode/             # Go-based CLI agent
│   ├── aider/                # Python pair programming agent
│   └── plandex/              # Go-based long-task agent
├── datasets/                 # Downloaded benchmark datasets
│   └── README.md             # Dataset notes & download instructions
├── harbor/                   # Harbor eval framework integration
│   └── README.md             # Harbor setup & usage guide
└── evaluation/              # Eval results & comparisons
    └── README.md
```

## Agents Under Comparison

| Agent | Language | Minimal Bash? | SWE-bench Score | Sandbox Support | Eval Framework |
|-------|----------|---------------|-----------------|-----------------|----------------|
| **mini-swe-agent** | Python | ✅ ~100 lines | >74% | Docker/Podman/Singularity/Bubblewrap | SWE-bench verified |
| **Terminus-2** | Python | ❌ Full toolkit | Unknown | Docker + Harbor | ✅ Harbor native |
| **mini-ts-swe-agent** | TypeScript | ❌ LangGraph (~400+ lines) | Unknown | Likely | Not benchmarked |
| **OpenCode** | Go | ❌ TUI + SQLite | None published | No | No |
| **Aider** | Python | ❌ Tree-sitter repo-map | None published | No | No |
| **Plandex** | Go | ❌ Plan management | None published | No | No |

## Key Finding

**mini-swe-agent is the only minimal bash-only agent** with a >74% SWE-bench verified score. Terminus-2 is the most developed alternative built into Harbor's eval framework, but it is NOT minimal — it has extensive toolkit support, tmux sessions, and terminal recording.

## Harbor Eval Framework

[Harbor](https://github.com/harbor-ai/harbor) is the eval framework used by Terminus-2. It supports:

- `harbor run -d "<org/name>" -m "<model>" -a "<agent>"` — run registered datasets
- `harbor run -p "<path>" -m "<model>" -a "<agent>"` — run local datasets
- Cloud sandbox via Daytona with parallel trial execution

Install: `uv tool install harbor` or `pip install harbor`

## Terminal-Bench Datasets (to download)

### Terminal-Bench 2.0
Located on HuggingFace. Requires authentication for clone:

```bash
# Install git-lfs
git lfs install

# Clone datasets (requires HF auth for some)
git clone https://huggingface.co/datasets/DCAgent2/terminal_bench_2_a1_nebius_swe_agent
git clone https://huggingface.co/datasets/DCAgent2/DCAgent2_terminal_bench_2_SWE-bench_SWE-agent-LM-32B_20251202_122851
git clone https://huggingface.co/datasets/DCAgent2/DCAgent2_terminal_bench_2_DCAgent_freelancer-t512s-32ep_Qwen3-8B_20251120_154017
```

### Related Datasets Found
- `mlfoundations-dev/gsm8k-terminal-bench-traces-qwen3-8b` — terminal traces
- `DCAgent2/terminal_bench_2_DCAgent_staqc-sandboxes-traces-terminus-2-*` — Terminus-2 sandbox traces
- `reasoning-degeneration-dev/wingdings-terminal-bench-2.0-mini-swe-agent-Qwen3-Next-80B-A3B-Instruct-20260220` — mini-swe-agent on terminal-bench 2.0

## Contributing

This repo is used to house analysis of Yudai SWE-Agent (yudai-swe-agent) compared against mini-swe-agent, Terminus-2, and other terminal-based coding agents.

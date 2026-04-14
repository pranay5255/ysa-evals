# Terminal-Bench Datasets

## Download Instructions

Some datasets require HuggingFace authentication. Clone with:

```bash
git lfs install
GIT_LFS_SKIP_SMUDGE=1 git clone https://huggingface.co/datasets/<dataset> <local_path>
cd <local_path>
git lfs pull
```

## Terminal-Bench 2.0 Datasets

### On HuggingFace (requires auth)

| Dataset | Description | Size |
|---------|-------------|------|
| `DCAgent2/terminal_bench_2_a1_nebius_swe_agent` | 265 rows, 31 tasks | 31 |
| `DCAgent2/DCAgent2_terminal_bench_2_SWE-bench_SWE-agent-LM-32B_20251202_122851` | SWE-bench SWE-agent LM 32B traces | 7 rows |
| `DCAgent2/DCAgent2_terminal_bench_2_DCAgent_freelancer-t512s-32ep_Qwen3-8B_20251120_154017` | DCAgent traces | 3.92k |
| `DCAgent2/DCAgent2_terminal_bench_2_Qwen_Qwen3-32B_20251120_143813` | Qwen3 32B on terminal-bench 2 | 1.53k |

### Mini-SWE-Agent Traces

| Dataset | Description |
|---------|-------------|
| `reasoning-degeneration-dev/wingdings-terminal-bench-2.0-mini-swe-agent-Qwen3-Next-80B-A3B-Instruct-20260220` | mini-swe-agent on terminal-bench 2.0 |

### Related Traces

| Dataset | Description |
|---------|-------------|
| `mlfoundations-dev/gsm8k-terminal-bench-traces-qwen3-8b` | GSM8K terminal-bench traces |
| `DCAgent2/dcagent2-terminal-bench-2-dcagent-staqc-sandboxes-traces-terminus-2-*` | Terminus-2 sandbox traces |

## Notes

- All Terminal-Bench datasets use the same format: rows of agent trajectories with observations
- Terminus-2 datasets include sandbox traces with execution logs
- mini-swe-agent traces on Terminal-Bench 2.0 are available at the reasoning-degeneration-dev dataset

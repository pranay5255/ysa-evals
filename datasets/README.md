# Terminal-Bench Datasets

## Downloaded Datasets

### ✅ terminal-bench-2.0 (85 MB)
**harborframework/terminal-bench-2.0** — 91 tasks, cloned successfully
```bash
git clone https://huggingface.co/datasets/harborframework/terminal-bench-2.0
```

Contains tasks like: `adaptive-rejection-sampler`, `build-cython-ext`, `fix-git`, `sql-database-setup`, etc.
Each task has: `instruction.md`, `task.toml`, `environment/`, `tests/`, `solution/`

### ✅ terminal-bench-2-leaderboard (13 GB)
**harborframework/terminal-bench-2-leaderboard** — Contains submissions from various agents on the leaderboard
```bash
git clone https://huggingface.co/datasets/harborframework/terminal-bench-2-leaderboard
```

### ⚠️ DCAgent2 terminal_bench_2 (LFS pointer only — not fully downloaded)
**DCAgent2/terminal_bench_2_a1_nebius_swe_agent** — 265 rows, 31 tasks
- Clone succeeded but LFS files were not pulled (423 byte pointer files)
- `git lfs pull` needed after clone

```bash
GIT_LFS_SKIP_SMUDGE=0 git clone https://huggingface.co/datasets/DCAgent2/terminal_bench_2_a1_nebius_swe_agent
cd terminal_bench_2_a1_nebius_swe_agent
git lfs pull
```

### Partial Clone Available
**DCAgent2/DCAgent2_terminal_bench_2_SWE-bench_SWE-agent-LM-32B_20251202_122851** — LFS pointer only (244 KB)

## Clone All Datasets

```bash
# Install git-lfs first
git lfs install

# Clone all three
git clone https://huggingface.co/datasets/harborframework/terminal-bench-2.0
git clone https://huggingface.co/datasets/harborframework/terminal-bench-2-leaderboard
GIT_LFS_SKIP_SMUDGE=0 git clone https://huggingface.co/datasets/DCAgent2/terminal_bench_2_a1_nebius_swe_agent
```

## Terminal-Bench 2.0 Task Structure

Each task directory contains:
- `instruction.md` — Task description
- `task.toml` — Task configuration (timeout, difficulty, etc.)
- `environment/` — Setup files (Dockerfile, scripts, data)
- `tests/` — Verification tests
- `solution/` — Reference solution

## Key Datasets from Search

| Dataset | Description | Status |
|---------|-------------|--------|
| `harborframework/terminal-bench-2.0` | Main benchmark, 91 tasks | ✅ Cloned (85MB) |
| `harborframework/terminal-bench-2-leaderboard` | Agent submissions | ✅ Cloned (13GB) |
| `DCAgent2/terminal_bench_2_a1_nebius_swe_agent` | 265 rows, 31 tasks | ⚠️ LFS pointer |
| `DCAgent2/terminal_bench_2` | Terminal-bench 2 main | 261 downloads |
| `zai-org/terminal-bench-2-verified` | Verified version | 1987 downloads |

## Note on LFS

Some datasets use Git LFS for large binary files. Clone with:
```bash
git lfs install
git lfs pull  # after clone
```

Or skip LFS content temporarily:
```bash
GIT_LFS_SKIP_SMUDGE=1 git clone <url>
```

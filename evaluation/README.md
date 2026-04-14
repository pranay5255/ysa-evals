# Evaluation Results

## Pending

Run results will be stored here after executing evaluations.

## Planned Experiments

1. **mini-swe-agent vs Terminus-2 on Terminal-Bench Lite subset**
2. **Cold-start time comparison** (Go binaries vs Python startup)
3. **Swapability test**: Can mini-ts-swe-agent replace mini-swe-agent in same sandbox?
4. **Harbor eval run** on a small dataset subset

## How to Add Results

```bash
# Run mini-swe-agent on SWE-bench subset
mini --model gpt-4o -d <repo_url> --output轨迹.json

# Run Terminus-2 via Harbor
harbor run -d "<dataset>" -m "<model>" -a "terminus-2" -n 4
```

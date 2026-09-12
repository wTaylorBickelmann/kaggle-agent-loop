# Coding conventions (this repo + experiments it drives)

Token thrift is a feature. Models that wander into `logs/` burn the budget.

## Planner whitelist

The planner may see **only** paths listed in `config/planner_reads.yaml`.
The orchestrator inlines those files. Do not add `logs/`, `ledger/runs/`,
data dumps, or notebooks to that list.

## Ledgers stay small

| File | Role | Mutation |
|------|------|----------|
| `ledger/STRATEGIES.md` | id / date / phase / one-liner | append one row per plan |
| `ledger/RESULTS.md` | id / status / cv / lb / notes | append one row per run |
| `ledger/CURRENT_STRATEGY.md` | executable spec | **rewrite in full** each plan |
| `ledger/runs/<id>.json` | metrics-only JSON | write after each run; never planner-visible |
| `logs/<id>.log` | long traces | disk only; never chat or RESULTS |

## Python in this repo

- Small functions, obvious names, few layers of abstraction.
- Adapters are thin shell / HTTP wrappers. No TUI automation.
- Stdlib + PyYAML only in the runtime package.
- Prefer `--dry-run` mocks over hitting real CLIs in CI.

## Experiment code (competition checkout)

- CV is ground truth. Record LB only after a real submit.
- Keep train/eval scripts deterministic (`seed` in the strategy spec).
- Write OOF / models under the competition repo or `logs/`, not into ledgers.
- Follow Deotte phase order unless RESULTS show a reason to jump: EDA → baseline → FE → stack.

## CLI agents

- Planner primary: `agy` headless (`--model` **before** `-p`). See `agy models`.
- Planner fallback: OpenAI-compatible DeepSeek (`DEEPSEEK_BASE_URL`).
- Executor: `qwen -p` with `OPENAI_BASE_URL` / `OPENAI_MODEL` pointing at local Qwen ~27B.
- Do not drive these through Cursor for the long loop.

# Bridge: `ev-purchase-kaggle` (S6E9)

This loop repo is competition-agnostic. It does **not** clone or vendor
`ev-purchase-kaggle`. Point it at a checkout you already have.

## Wire-up

```bash
# sibling layout (typical on a Mac Studio)
# ~/src/kaggle-agent-loop
# ~/src/ev-purchase-kaggle

cd ~/src/kaggle-agent-loop
cp .env.example .env
```

In `.env`:

```bash
COMPETITION_ROOT=/absolute/path/to/ev-purchase-kaggle
```

Or in `config/loop.yaml`:

```yaml
competition:
  name: playground-s6e9-ev-purchase
  root: ${COMPETITION_ROOT:-/absolute/path/to/ev-purchase-kaggle}
  metric: roc_auc
  higher_is_better: true
  target: Will_Buy_EV
```

## Planner reads

`config/planner_reads.yaml` already lists common entrypoints
(`src/train.py`, `src/features.py`, `train.py`, …). The orchestrator
resolves each optional path against `competition_root` first, then the
loop root, and **skips missing files**. Add or remove paths to match
the actual filenames in your EV checkout.

Do not add notebooks, `outputs/`, or `logs/` to that list.

## Executor cwd

`qwen` runs with cwd = `competition_root` when that directory exists,
and is also given `--include-directories <loop_root>` so it can write
`ledger/RESULTS.md` and `logs/<id>.log` here.

## Data

Keep `train.csv` / `test.csv` inside the competition checkout (or a
gitignored `data/` folder). Never copy competition CSVs into this repo
if you want the planner whitelist to stay small.

## First real iteration (not dry-run)

```bash
# inspect what the planner will see
python -m loop show-whitelist

# optional: execute the starter LightGBM baseline as-is
python -m loop execute-once

# or let the planner rewrite CURRENT_STRATEGY then execute
python -m loop run --iterations 5
```

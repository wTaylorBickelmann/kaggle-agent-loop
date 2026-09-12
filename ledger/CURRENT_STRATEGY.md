# s001 — LightGBM 5-fold stratified baseline

Playground Series S6E9 (Predicting Electric Vehicle Purchases) is the default
example. The same spec shape works for any tabular Playground-style task:
swap `competition.root`, `metric`, and the target column in `config/loop.yaml`.

## Hypothesis

A plain LightGBM on the provided columns with stratified 5-fold CV is enough
to establish a CV ground-truth baseline before feature engineering.

## Changes

- Entry point: `train.py` or `src/train.py` under `competition_root` (e.g. ev-purchase-kaggle).
- Model: LightGBM, modest trees (`n_estimators≈400`, `learning_rate≈0.05`).
- CV: StratifiedKFold `n_splits=5`, `shuffle=True`, fixed seed.
- Target: `Will_Buy_EV`. Metric: ROC AUC (higher is better).
- Categorical columns: native categorical dtype or label-encode; no target encoding yet.
- Do not touch stacking or heavy FE in this run.

## Acceptance

- Produce a mean OOF AUC and per-fold scores.
- Write long output only to `logs/s001.log`.
- Append one RESULTS row; write `ledger/runs/s001.json` (metrics only).

## Logging

- Never paste fold arrays, traces, or notebooks into the ledger.

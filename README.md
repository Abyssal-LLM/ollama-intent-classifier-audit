# Ollama Intent Classifier — Audit Data

Raw per-call audit results for each published model, from the full benchmark: **6,076 calls per model** on a hand-written corpus of **243 intents across 22 domains** with **zero message overlap with any training data**.

## Live Dashboard

**[View the interactive dashboard](https://abyssal-llm.github.io/ollama-intent-classifier-audit/)**

Browse raw CSV data, view computed accuracy/latency metrics, and compare models side-by-side — all client-side, no server required.

## Models

| File | Model | Description |
| --- | --- | --- |
| `audit_stock-qwen2.5-1.5b.csv` | `qwen2.5:1.5b` | Stock baseline every model is compared against |
| `audit_intent-classifier-general-v2.csv` | `Abyssal/intent-classifier-general-v2` | v2 |
| `audit_intent-classifier-general-v3.csv` | `Abyssal/intent-classifier-general-v3` | v3 |
| `audit_intent-classifier-general-overall.csv` | `Abyssal/intent-classifier-general-overall` | Overall |
| `audit_intent-classifier-general-oav2.csv` | `Abyssal/intent-classifier-general-oav2` | OA v2 |
| `audit_intent-classifier-general-acv1.csv` | `Abyssal/intent-classifier-general-acv1` | AC v1 |
| `audit_intent-classifier-general-accuracy.csv` | `Abyssal/intent-classifier-general-accuracy` | Accuracy |

## Columns

`case_id, variant, mode, draw, message, gold, candidates, expected, prompt, output_raw, output_norm, matched, in_list, correct, latency_ms, tokens`

| Column | Description |
| --- | --- |
| `variant` | `native_in_random` — correct intent + random distractors · `native_in_nearsyn` — correct + near-synonym trap · `custom_in` — invented intent names · `native_out` — correct intent deliberately withheld · `exact` — exact intent phrase |
| `mode` | `free` (plain generation) or `enum` (JSON-schema constrained decoding) |
| `in_list` | Whether the answer came from the offered candidate list |
| `correct` | For `native_out` this means *escaping* the list (correctly refusing), not picking |

## Reproduce

```bash
# Per-call audit
python test_ollama_v2.py --model <model> --out <file>.csv

# Comparison table
python score_audit.py <file1>.csv <file2>.csv ...
```

## Dashboard Features

- **Raw Data** — Virtual-scrolled table of all 48k+ rows per model, sortable columns, search/filter
- **Results** — Computed accuracy, rejection rate, latency (avg/median/p95), per-variant and per-mode breakdowns
- **Compare** — Select any models for side-by-side metrics comparison with best/worst highlighting
- **Diff View** — Split-screen comparison showing exactly where two models disagree

## License

Research use. Audit data provided as-is.

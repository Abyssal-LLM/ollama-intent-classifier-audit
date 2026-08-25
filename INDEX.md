# Raw audit data

Per-call results for each published model, from the full audit: **6,076 calls per model**, on a
hand-written corpus of **243 intents across 22 domains** with **zero message overlap with any
training data** (verified). One row per API call.

| File | Model |
| --- | --- |
| `audit_stock-qwen2.5-1.5b.csv` | stock `qwen2.5:1.5b` — the baseline every model is compared against |
| `audit_intent-classifier-general-v2.csv` | `Abyssal/intent-classifier-general-v2` |
| `audit_intent-classifier-general-v3.csv` | `Abyssal/intent-classifier-general-v3` |
| `audit_intent-classifier-general-overall.csv` | `Abyssal/intent-classifier-general-overall` |
| `audit_intent-classifier-general-oav2.csv` | `Abyssal/intent-classifier-general-oav2` |
| `audit_intent-classifier-general-acv1.csv` | `Abyssal/intent-classifier-general-acv1` |
| `audit_intent-classifier-general-accuracy.csv` | `Abyssal/intent-classifier-general-accuracy` |

Not included: `intent-classifier-general` (v1) — it was only ever run on the older 847-call corpus,
so there is no full-corpus file for it.

## Columns

`case_id, variant, mode, draw, message, gold, candidates, expected, prompt, output_raw,
output_norm, matched, in_list, correct, latency_ms, tokens`

- **`variant`**
  - `native_in_random` — correct intent offered alongside random distractors
  - `native_in_nearsyn` — correct intent offered alongside a forced near-synonym trap
  - `custom_in` — the same intents renamed to invented names the model has never seen
  - `native_out` — the correct intent is **deliberately withheld**
  - `exact` — the message is an exact intent phrase
- **`mode`** — `free` (plain generation) or `enum` (JSON-schema constrained decoding)
- **`in_list`** — whether the answer came from the offered candidate list
- **`matched`** — identical to `in_list` in every row of every file in this directory; kept for
  backwards compatibility. Prefer `in_list`.
- **`correct`** — a correct in-scope pick. It is `False` on **every** `native_out` row, including the
  rows where the model refused correctly: refusal is *not* scored in this column. To score the refusal
  probes, use `in_list == False` (a clean escape from the list); rejection rate is the share of
  `native_out` rows with `in_list == False`.

## Reproduce

```
python test_ollama_v2.py --model <model> --out <file>.csv     # per-call audit
python score_audit.py <file1>.csv <file2>.csv ...             # comparison table
```

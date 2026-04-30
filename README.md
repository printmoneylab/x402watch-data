# x402watch-data

Daily snapshots of the [x402](https://x402.org) ecosystem published by
[PrintMoneyLab](https://printmoneylab.com) /
[x402.printmoneylab.com](https://x402.printmoneylab.com).

Data is regenerated every UTC 04:00 (KST 13:00) from x402watch's indexers
running on Oracle Cloud and pushed to this repo automatically.

## Files

Each day, four files land in `data/`:

| File | Format | Compressed | Contents |
|---|---|:---:|---|
| `services-{YYYY-MM-DD}.json` | JSON | gzip | Active services snapshot — name, description, category, price, organic/wash percentages |
| `wash-flagged-{YYYY-MM-DD}.json` | JSON | gzip | Buyers labelled `self_test` or `suspected_wash`, with confidence and reason |
| `category-benchmarks-{YYYY-MM-DD}.json` | JSON | — | Per-category aggregate stats: service count, price percentiles, real-vs-wash breakdown |
| `buyer-labels-{YYYY-MM-DD}.json` | JSON | — | Label distribution + last 7 days of label-change events + 30d real-vs-wash volume |

Each file has a `schema_version` field so consumers can pin against breakage.

## Methodology

Wash-filter and category-classification methodology lives in the parent repo:
- [printmoneylab/x402watch — wash-filter-methodology.md](https://github.com/printmoneylab/x402watch/blob/main/docs/wash-filter-methodology.md)

## License

[CC0 1.0 Universal](LICENSE) — public domain dedication. Use freely, no
attribution required (but we'd appreciate it).

## Loading examples

```python
import gzip, json
from pathlib import Path

# Services snapshot
with gzip.open("data/services-2026-04-30.json.gz", "rt") as f:
    services = json.load(f)["services"]
print(f"Active services: {len(services)}")
print(f"Top category: {max(set(s['category'] for s in services), key=lambda c: sum(1 for s in services if s['category']==c))}")
```

```sh
# CLI quick look
zcat data/services-2026-04-30.json.gz | jq '.service_count'
jq '.real_vs_wash_30d' data/buyer-labels-2026-04-30.json
```

## Issues / questions

Operator dispute on a `self_test` or `suspected_wash` label, or any other
feedback: open an issue here or reach PrintMoneyLab via Telegram.

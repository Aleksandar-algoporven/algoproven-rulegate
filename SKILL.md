---
name: algoproven-rulegate
description: Pre-trade prop firm rule enforcement for futures traders. Checks whether a proposed trade is safe given your prop firm's drawdown rules. Supports Topstep, Apex, FFN. Returns ALLOW / REDUCE / BLOCK with the maximum safe position size.
---

# AlgoProven RuleGate

Pre-trade safety check for prop firm futures traders. Before placing an order, ask RuleGate whether it's safe — it enforces your firm's drawdown limits and tells you the maximum safe size.

## Quick check (no signup required)

```bash
curl -s -X POST "https://app.algoproven.com/api/v1/rulegate/check" \
  -H "Content-Type: application/json" \
  -d '{
    "firm": "topstep_50k",
    "symbol": "MES",
    "qty": 2,
    "stop_ticks": 4,
    "room": 800
  }'

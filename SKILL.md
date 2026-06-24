---
name: algoproven-rulegate
description: Pre-trade prop firm rule enforcement for futures traders. Checks whether a proposed trade is safe given your prop firm's drawdown rules. Supports Topstep, Apex, FFN. Returns ALLOW / REDUCE / BLOCK with the maximum safe position size.
---

# AlgoProven RuleGate

Pre-trade safety check for prop firm futures traders. Before placing an order, ask RuleGate whether it's safe — it enforces your firm's drawdown limits and tells you the maximum safe size.

## Quick check (no signup required)

```bash
# Can I take this trade?
curl -s -X POST "https://app.algoproven.com/api/v1/rulegate/check" \
  -H "Content-Type: application/json" \
  -d '{
    "firm": "topstep_50k",
    "symbol": "MES",
    "qty": 2,
    "stop_ticks": 4,
    "room": 800
  }'
```

**Parameters:**
- `firm`: `topstep_50k` | `topstep_100k` | `topstep_150k` | `apex_50k` | `ffn_50k`
- `symbol`: `MES` | `MNQ` | `MGC` | `MCL` | `M6E` | `ES` | `NQ`
- `qty`: number of contracts
- `stop_ticks`: stop distance in ticks
- `room`: $ remaining before you breach the daily loss limit (e.g. $800 of your $1,000 Topstep DLL)

**Response:**
```json
{
  "decision": "ALLOW",
  "reason": "Risk $10 is within $800 of room — $790 left after.",
  "max_safe_qty": null,
  "proposed_risk": 10.0,
  "room": 800.0,
  "distance_to_breach": 790.0
}
```

Decisions: `ALLOW` (safe), `REDUCE` (cut size to `max_safe_qty`), `BLOCK` (no safe size).

## List supported firms

```bash
curl -s "https://app.algoproven.com/api/v1/rulegate/firms"
```

## Advanced: p95 size-down (requires API key)

Uses Bottester-validated p95 trough data to compute the maximum safe size across your whole open book, accounting for correlated legs and the Topstep ratchet.

```bash
curl -s -X POST "https://app.algoproven.com/api/v1/rulegate/max-safe-size" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "firm": "topstep_50k",
    "symbol": "MES",
    "requested_qty": 2,
    "dist_to_line": 500,
    "tier": "p95",
    "open_book": []
  }'
```

Get your API key at https://app.algoproven.com — free tier included with any AlgoProven plan.

## Usage in Claude

When a user asks "can I take this trade?" or "what's the max safe size?":

1. Ask for: firm, symbol, stop (ticks), and room remaining ($ to daily limit breach)
2. Call `/check` with those values
3. Report the decision and reason in plain English
4. If REDUCE: tell them the max safe size
5. If BLOCK: suggest tightening the stop or waiting for more room

## When to use each endpoint

| endpoint | use case | auth |
|---|---|---|
| `/check` | quick rule-math check with known stop + room | none |
| `/firms` | list available firm presets | none |
| `/max-safe-size` | p95 joint sizing across open book | API key |

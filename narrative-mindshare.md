# Narrative Mindshare API (Pay-As-You-Go)

Returns daily time series mindshare data for a crypto narrative (e.g. "AI", "DeFi", "L2"). Measures the proportion of tweets mentioning a narrative relative to the overall crypto market.

> See [README](./README.md) for base URL, payment protocol, and pricing.

## Endpoint

```
GET /api/payg/narrative_mindshare
```

## Authentication

No API key required. Payment via x402 protocol. See [README](./README.md).

## Pricing

**$0.02 per data point** (date key in the `narrative_mindshare` object). Minimum charge: $0.02.

For example, a 30-day date range returning 30 data points costs $0.60.

**Billing rules:**
- If the API request fails (server error), no charge is incurred.
- If the API request succeeds but returns an empty result, the minimum price is still charged.

> If you would like to purchase in bulk, please contact us on Telegram at @kaitoai2022

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `narrative` | Yes | - | Narrative ID or name (e.g. `AI`, `DeFi`). See [/narratives](./narratives.md) for the full list. |
| `start_date` | No | 30 days ago | Start date in `YYYY-MM-DD` format. Earliest: `2023-01-01`. |
| `end_date` | No | Tomorrow | End date in `YYYY-MM-DD` format. |

## Narratives List

To get all available narratives, see [narratives.md](./narratives.md) or call:

```
GET /api/v1/narratives
```

## Example

**Step 1** — Get price:

```bash
curl -s 'https://api.kaito.ai/api/payg/narrative_mindshare?narrative=AI&start_date=2024-01-01&end_date=2024-01-31'
# Returns 402 with payment requirements
```

**Step 2** — Pay and get data (using x402 SDK, see [README](./README.md)):

```bash
curl -s 'https://api.kaito.ai/api/payg/narrative_mindshare?narrative=AI&start_date=2024-01-01&end_date=2024-01-31' \
  -H 'Payment-Signature: <base64-encoded payment payload>'
```

## Response

```json
{
  "narrative_mindshare": {
    "2024-01-01": 0.08,
    "2024-01-02": 0.09
  }
}
```

## Response Fields

- **narrative_mindshare**: Object keyed by date (`YYYY-MM-DD`). Each value is a decimal representing the narrative's share of crypto Twitter conversation.

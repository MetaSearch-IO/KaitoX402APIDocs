# Sentiment API (Pay-As-You-Go)

Returns daily time series sentiment data for a cryptocurrency token.

> See [README](./README.md) for base URL, payment protocol, and pricing.

## Endpoint

```
GET /api/payg/sentiment
```

## Authentication

No API key required. Payment via x402 protocol. See [README](./README.md).

## Pricing

**$0.02 per data point** (date key in the `sentiment` object). Minimum charge: $0.02.

For example, a 30-day date range returning 30 data points costs $0.60.

**Billing rules:**
- If the API request fails (server error), no charge is incurred.
- If the API request succeeds but returns an empty result, the minimum price is still charged.

> If you would like to purchase in bulk, please contact us on Telegram at @kaitoai2022

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `token` | Yes | - | Token ticker (e.g. `BTC`, `ETH`). Use [/tokens](./tokens.md) to list available tokens. |
| `start_date` | No | 30 days ago | Start date in `YYYY-MM-DD` format. Earliest: `2020-01-01`. |
| `end_date` | No | Tomorrow | End date in `YYYY-MM-DD` format. |
| `adjusted` | No | `true` | When `true`, includes replies/quotes related to the token, excludes organizational accounts, and weights by smart engagement. |
| `average` | No | `false` | When `true`, returns average sentiment (-1 to +1). When `false`, returns absolute volume-weighted sentiment. |
| `gaussian` | No | `true` | When `true`, applies Gaussian smoothing to the sentiment time series. |
| `language` | No | `all` | Language filter. Values: `all`, `en`, `zh`, `ko`, `others`. |
| `version` | No | `3` | Sentiment model version. Values: `2`, `3`. |

## Example

**Step 1** — Get price:

```bash
curl -s 'https://api.kaito.ai/api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31'
# Returns 402 with payment requirements
```

**Step 2** — Pay and get data (using x402 SDK, see [README](./README.md)):

```bash
curl -s 'https://api.kaito.ai/api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31' \
  -H 'Payment-Signature: <base64-encoded payment payload>'
```

## Response

```json
{
  "sentiment": {
    "2024-01-01": {
      "score": 0.65,
      "event": "Bitcoin ETF approval speculation intensifies"
    },
    "2024-01-02": {
      "score": 0.72,
      "event": ""
    }
  }
}
```

## Response Fields

- **sentiment**: Object keyed by date (`YYYY-MM-DD`).
  - **score**: Sentiment score. Volume-weighted bullishness/bearishness across all tweets mentioning the token.
  - **event**: Description of a notable event on that date (empty string if none).

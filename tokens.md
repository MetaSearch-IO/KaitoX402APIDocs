# Tokens List API

Returns a list of all supported token tickers. This endpoint does **not** require authentication.

> See [README](./README.md) for API overview and pricing.

## Endpoint

```
GET /api/v1/tokens
```

## Authentication

No API key required.

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `scope` | No | - | Set to `pretge` to return only Pre-TGE Arena tokens. |

## Example

```bash
curl -X 'GET' \
  'https://api.kaito.ai/api/v1/tokens'
```

## Response

```json
[
  {
    "token": "BTC",
    "fullname": "Bitcoin",
    "symbol": "BTC",
    "coingecko_id": "bitcoin"
  },
  {
    "token": "ETH",
    "fullname": "Ethereum",
    "symbol": "ETH",
    "coingecko_id": "ethereum"
  }
]
```

## Response Fields

Each item in the array contains:

- **token**: Internal token ticker ID.
- **fullname**: Full project name.
- **symbol**: Display ticker symbol.
- **coingecko_id**: CoinGecko identifier (may be `null`).

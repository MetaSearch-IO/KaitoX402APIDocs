# Smart Followers API (Pay-As-You-Go)

Returns smart follower data for a Twitter user. Supports two modes: `count` returns the cumulative smart follower count, `users` returns the list of smart followers gained on a specific date.

> See [README](./README.md) for base URL, payment protocol, and pricing.

## Endpoint

```
GET /api/payg/smart_followers
```

## Authentication

No API key required. Payment via x402 protocol. See [README](./README.md).

## Pricing

**$0.20 per request (flat).**

Both `count` and `users` modes cost $0.20 per request.

**Billing rules:**
- If the API request fails (server error), no charge is incurred.
- If the API request succeeds but returns an empty result, the minimum price is still charged.

> If you would like to purchase in bulk, please contact us on Telegram at @kaitoai2022

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `user_id` | No* | - | Twitter user ID. *One of `user_id` or `username` is required. |
| `username` | No* | - | Twitter username. *One of `user_id` or `username` is required. |
| `date` | No | Yesterday | Date in `YYYY-MM-DD` format. Must be `>= 2024-01-01` and before today. |
| `mode` | No | `count` | Response mode. `count`: returns cumulative smart follower count. `users`: returns list of smart followers for the given date. |

## Example 1: Count Mode

**Step 1** — Get price:

```bash
curl -s 'https://api.kaito.ai/api/payg/smart_followers?username=VitalikButerin&mode=count'
# Returns 402 with payment requirements ($0.20)
```

**Step 2** — Pay and get data (using x402 SDK, see [README](./README.md)):

```bash
curl -s 'https://api.kaito.ai/api/payg/smart_followers?username=VitalikButerin&mode=count' \
  -H 'Payment-Signature: <base64-encoded payment payload>'
```

### Response

```json
{
  "user_id": "123456789",
  "mode": "count",
  "date": "2024-06-15",
  "num_of_smart_followers": 1234,
  "current_ict": true
}
```

## Example 2: Users Mode

**Step 1** — Get price:

```bash
curl -s 'https://api.kaito.ai/api/payg/smart_followers?username=VitalikButerin&date=2024-06-15&mode=users'
# Returns 402 with payment requirements ($0.20)
```

**Step 2** — Pay and get data:

```bash
curl -s 'https://api.kaito.ai/api/payg/smart_followers?username=VitalikButerin&date=2024-06-15&mode=users' \
  -H 'Payment-Signature: <base64-encoded payment payload>'
```

### Response

```json
{
  "user_id": "123456789",
  "mode": "users",
  "date": "2024-06-15",
  "smart_followers": [
    { "user_id": "111111111", "username": "alice" },
    { "user_id": "222222222", "username": "bob" }
  ]
}
```

## Response Fields

- **user_id**: Twitter user ID.
- **mode**: The requested mode.
- **date**: The date used for the query.
- **num_of_smart_followers**: (count mode) Cumulative smart follower count.
- **current_ict**: (count mode) Whether the user is a recognized KOL.
- **smart_followers**: (users mode) Array of smart followers with `user_id` and `username`.

# Kaito PAYG API Documentation

> **NOTE:** This API is currently in preview. For questions, contact us via [Telegram](https://t.me/kaitoai2022) or email at api@kaito.ai.

## Overview

The Pay-As-You-Go (PAYG) API provides access to Kaito endpoints **without an API key**. Instead of subscription-based authentication, each request is paid for individually using the [x402 payment protocol](https://www.x402.org/) — crypto micropayments via USDC on Base mainnet.

PAYG endpoints provide identical parameters and response formats to their counterparts in the Kaito API.

## Base URL

```
https://api.kaito.ai/api/payg/
```

## Authentication

No API key is required. Payment is handled via the x402 protocol using USDC on **Base mainnet**.

There are no rate limits — you pay per request.

## Free Reference Endpoints

The following endpoints are free (no API key or payment required) and useful when building queries:

| Endpoint | Description | Doc |
|----------|-------------|-----|
| `GET /api/v1/tokens` | List all supported token tickers | [tokens.md](./tokens.md) |
| `GET /api/v1/narratives` | List all supported narrative IDs | [narratives.md](./narratives.md) |

## How x402 Payment Works

PAYG uses **dynamic per-item pricing**: the price depends on the number of items in the response. The payment flow has two steps:

### Step 1: Price Discovery

Make a regular GET request without any payment header. The server executes your query, counts the billable items, and returns a `402 Payment Required` response with the computed price:

```
GET /api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31

HTTP/1.1 402 Payment Required
```

```json
{
  "x402Version": 2,
  "error": "Payment required",
  "resource": {
    "url": "https://api.kaito.ai/api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31",
    "description": "Pay-as-you-go API access",
    "mimeType": "application/json"
  },
  "accepts": [
    {
      "scheme": "exact",
      "network": "base:8453",
      "maxAmountRequired": "620000",
      "resource": "https://api.kaito.ai/api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31",
      "description": "Pay-as-you-go API access",
      "mimeType": "application/json",
      "payTo": "0x...",
      "maxTimeoutSeconds": 60,
      "asset": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
      "extra": {
        "name": "USDC",
        "version": "2"
      }
    }
  ]
}
```

> The `maxAmountRequired` is in USDC atomic units (6 decimals). For example, `620000` = $0.62.

### Step 2: Paid Request

Using the x402 SDK, create an EIP-3009 `transferWithAuthorization` signature for the required amount, then re-send the same request with the `Payment-Signature` header:

```
GET /api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31
Payment-Signature: <base64-encoded payment payload>

HTTP/1.1 200 OK
```

The server verifies the payment, returns the API response, and settles the USDC transfer via the x402 facilitator.

> The server caches the response from Step 1 for 60 seconds. If you send the paid request within that window, the server returns the cached result without re-executing the query.

## Pricing

All prices are in USDC on Base mainnet. Pricing is **per billable item** with a minimum charge equal to one unit.

| Endpoint | Unit Price | Billed By | Minimum |
|----------|-----------|-----------|---------|
| [`sentiment`](./sentiment.md) | $0.02 | per data point (date) | $0.02 |
| [`mindshare`](./mindshare.md) | $0.02 | per data point (date) | $0.02 |
| [`narrative_mindshare`](./narrative-mindshare.md) | $0.02 | per data point (date) | $0.02 |
| [`smart_followers`](./smart-followers.md) | $0.20 | per request (flat) | $0.20 |

**Examples:**
- `sentiment` for a 30-day range (30 data points) → $0.60
- `smart_followers` → always $0.20 (flat per request)

**Billing rules:**
- If the API request fails (server error), no charge is incurred.
- If the API request succeeds but returns an empty result, the minimum price (1 data point) is still charged.

> If you would like to purchase in bulk, please contact us on Telegram at @kaitoai2022

## SDK Quick Start (TypeScript)

Install the x402 client SDK:

```bash
npm install @x402/core @x402/evm
```

Example using `@x402/core` and `@x402/evm`:

```typescript
import { x402Client, x402HTTPClient } from "@x402/core/client";
import { registerExactEvmScheme } from "@x402/evm/exact/client";
import { privateKeyToAccount } from "viem/accounts";

// Set up x402 client with your wallet
const account = privateKeyToAccount("0xYOUR_PRIVATE_KEY");
const client = new x402Client();
registerExactEvmScheme(client, { signer: account });
const httpClient = new x402HTTPClient(client);

const url =
  "https://api.kaito.ai/api/payg/sentiment?token=BTC&start_date=2024-01-01&end_date=2024-01-31";

// Step 1: Get payment requirements
const response402 = await fetch(url);
const paymentRequired = await response402.json();

// Step 2: Create payment payload (EIP-3009 signature)
const paymentPayload = await httpClient.createPaymentPayload(paymentRequired);

// Step 3: Encode and send paid request
const paymentHeaders = httpClient.encodePaymentSignatureHeader(paymentPayload);
const response = await fetch(url, { headers: paymentHeaders });
const data = await response.json();
```

## Endpoints

| Endpoint | Description | Doc |
|----------|-------------|-----|
| `GET /sentiment` | Time series sentiment scores for a token | [sentiment.md](./sentiment.md) |
| `GET /mindshare` | Time series mindshare data for a token | [mindshare.md](./mindshare.md) |
| `GET /narrative_mindshare` | Time series mindshare for a crypto narrative | [narrative-mindshare.md](./narrative-mindshare.md) |
| `GET /smart_followers` | Smart follower count or list for a user | [smart-followers.md](./smart-followers.md) |

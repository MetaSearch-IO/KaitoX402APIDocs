# Narratives List API

Returns a list of all supported crypto narratives. This endpoint does **not** require authentication.

> See [README](./README.md) for API overview and pricing.

## Endpoint

```
GET /api/v1/narratives
```

## Authentication

No API key required.

## Parameters

None.

## Example

```bash
curl -X 'GET' \
  'https://api.kaito.ai/api/v1/narratives'
```

## Response

```json
[
  {
    "narrative": "defi",
    "fullname": "DeFi",
    "description": "Decentralized Finance protocols and platforms"
  },
  {
    "narrative": "layer2",
    "fullname": "Layer 2",
    "description": "Layer 2 scaling solutions"
  }
]
```

## Response Fields

Each item in the array contains:

- **narrative**: Internal narrative identifier. Use this value with the `/narrative_mindshare` endpoint.
- **fullname**: Display name for the narrative.
- **description**: Brief description of the narrative category.

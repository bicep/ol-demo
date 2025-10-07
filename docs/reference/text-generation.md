# Text Generation API

Generate human-like text based on prompts.

## Endpoint
POST `/v1/text/generate`

## Authentication
All requests require a Bearer token in the `Authorization` header.

```http
Authorization: Bearer YOUR_API_KEY
Content-Type: application/json
```

## Request Body
```json
{
  "prompt": "Write a motivational quote about innovation.",
  "max_tokens": 100,
  "temperature": 0.7
}
```

## Response
```json
{
  "id": "txt-123",
  "object": "text_completion",
  "output": "Innovation is the art of turning ideas into reality..."
}
```

## Errors
- `401 Unauthorized` → Invalid API key
- `429 Too Many Requests` → Rate limit exceeded

# Rate Limiting

API keys are limited to **300 requests per minute**, tracked in a rolling window
per key. OAuth2 partner tokens are limited per `client_id` at a higher default of
**1,200 requests per minute** — contact support if your integration needs more.

## Response headers

Every response includes your current rate-limit status:

| Header | Meaning |
|---|---|
| `X-RateLimit-Limit` | Requests allowed per window |
| `X-RateLimit-Remaining` | Requests remaining in the current window |
| `X-RateLimit-Reset` | Unix timestamp when the window resets |

```
X-RateLimit-Limit: 300
X-RateLimit-Remaining: 287
X-RateLimit-Reset: 1757856000
```

## When you're rate limited

Exceeding the limit returns `429 Too Many Requests` with `error.code` set to
`rate_limit_exceeded`. Back off until the time in `X-RateLimit-Reset`, then retry.

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded. Retry after the time in X-RateLimit-Reset.",
    "details": null
  }
}
```

## Best practices

- Check `X-RateLimit-Remaining` proactively and slow down before you hit `0`,
  rather than reacting only after a `429`.
- For bulk operations (e.g. syncing many reservations), add a small delay between
  requests instead of firing them all at once.
- Use [webhooks](webhooks.md) instead of polling list endpoints for status changes —
  it's both faster for you and easier on your rate limit.

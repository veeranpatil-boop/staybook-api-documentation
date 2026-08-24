# Errors

Every error response has the same shape:

```json
{
  "error": {
    "code": "room_unavailable",
    "message": "The requested room has no availability for the given dates.",
    "details": null
  }
}
```

- **`code`** is a stable, machine-readable string. Match on this in your code, not
  on the HTTP status alone or on `message` text, which may change wording over time.
- **`message`** is a human-readable description, useful for logs and debugging.
- **`details`** is present on some errors (for example, validation errors) with
  field-level information. It's `null` when there's nothing more to add.

## HTTP status codes

| Status | Meaning |
|---|---|
| `400` | The request was malformed or failed validation. Check `error.details`. |
| `401` | Missing, invalid, or expired credentials. |
| `404` | The requested resource doesn't exist. |
| `409` | The request conflicts with the resource's current state (e.g. booking a room that was just taken, or cancelling an already-cancelled reservation). |
| `429` | Too many requests. See [Rate Limiting](rate-limiting.md). |
| `5xx` | Something went wrong on our end. Safe to retry with backoff. |

## Common error codes

| Code | Status | Meaning |
|---|---|---|
| `validation_error` | 400 | One or more request fields failed validation. See `error.details`. |
| `token_expired` | 401 | An OAuth2 access token has expired. Request a new one. |
| `invalid_api_key` | 401 | The API key is missing, revoked, or doesn't match the environment. |
| `room_unavailable` | 409 | The requested room has no availability for the given dates. |
| `reservation_not_modifiable` | 409 | The reservation isn't in `pending` or `confirmed` status. |
| `already_cancelled` | 409 | The reservation is already cancelled or completed. |

## Retrying safely

`POST /reservations` is not automatically idempotent — retrying a timed-out request
can create a duplicate reservation. If you need to retry a create request safely,
check `GET /reservations?status=pending` for a matching reservation before retrying,
or contact support about idempotency-key support on your account.

# Webhooks

Reservation status can change asynchronously — a payment authorization completes,
a guest cancels, a property confirms a pending booking. Webhooks notify your
application in real time instead of you having to poll `GET /reservations`.

## 1. Create a subscription

```bash
curl -X POST https://api.staybook.dev/v1/webhooks \
  -H "X-API-Key: sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://yourapp.com/webhooks/staybook",
    "events": ["reservation.confirmed", "reservation.cancelled", "payment.failed"]
  }'
```

## 2. Available events

| Event | Fires when |
|---|---|
| `reservation.created` | A reservation is created in `pending` status |
| `reservation.confirmed` | A reservation moves to `confirmed` (payment authorized) |
| `reservation.cancelled` | A reservation is cancelled, by either party |
| `payment.captured` | A payment is successfully captured |
| `payment.failed` | A payment attempt fails |

## 3. Payload shape

```json
{
  "event": "reservation.confirmed",
  "created_at": "2026-08-24T09:12:00Z",
  "data": {
    "id": "res_7c21ad",
    "status": "confirmed",
    "property_id": "prop_9f8a2c",
    "room_id": "room_44b1",
    "check_in": "2026-09-14",
    "check_out": "2026-09-17"
  }
}
```

## 4. Verify the signature

Every webhook request includes an `X-StayBook-Signature` header — an HMAC-SHA256
of the raw request body, signed with your webhook signing secret (shown once when
you create the subscription). Verify it before trusting the payload:

```python
import hmac, hashlib

def verify_signature(payload_bytes, signature_header, signing_secret):
    expected = hmac.new(signing_secret.encode(), payload_bytes, hashlib.sha256).hexdigest()
    return hmac.compare_digest(expected, signature_header)
```

Reject the request (return a 4xx) if the signature doesn't match. This stops
attackers from sending fake booking events to your endpoint.

## 5. Respond quickly, retry on failure

Return a `2xx` status within 5 seconds of receiving the webhook. If your endpoint
is slow, do the heavy work asynchronously after responding.

If your endpoint doesn't respond with `2xx`, StayBook retries with exponential
backoff for up to 24 hours (5 attempts total), then marks the delivery as failed.
Past deliveries — including failed ones — are visible in
**Dashboard → Developers → Webhooks → Delivery Log**.

## 6. Design for idempotency

The same event can be delivered more than once (for example, if your endpoint
responds slowly and StayBook retries before your first response arrives). Use the
event's implicit uniqueness — reservation `id` plus `event` type — to ignore
duplicates you've already processed rather than assuming exactly-once delivery.

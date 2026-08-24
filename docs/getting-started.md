# Getting Started

The StayBook API lets you search property availability, create reservations, and
receive real-time booking events. This guide walks through the smallest possible
integration: searching availability and creating a reservation.

## 1. Get sandbox credentials

Every StayBook account has a sandbox environment with test properties and no real
charges. Generate a sandbox API key from **Dashboard → Developers → API Keys**.

## 2. Search for availability

```bash
curl "https://sandbox.api.staybook.dev/v1/availability?propertyId=prop_9f8a2c&checkIn=2026-09-14&checkOut=2026-09-17&guests=2" \
  -H "X-API-Key: sk_sandbox_xxx"
```

```json
{
  "data": [
    {
      "room": { "id": "room_44b1", "name": "Deluxe King", "max_guests": 2 },
      "nights": 3,
      "total_price": { "amount": 73500, "currency": "EUR" },
      "units_remaining": 4
    }
  ]
}
```

Availability isn't held — it can change between search and booking, so the create
step below can return a `409 Conflict` if another request claimed the last room first.

## 3. Create a guest profile

```bash
curl -X POST "https://sandbox.api.staybook.dev/v1/guests" \
  -H "X-API-Key: sk_sandbox_xxx" \
  -H "Content-Type: application/json" \
  -d '{"full_name": "Amara Okafor", "email": "amara@example.com"}'
```

## 4. Create the reservation

```bash
curl -X POST "https://sandbox.api.staybook.dev/v1/reservations" \
  -H "X-API-Key: sk_sandbox_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "room_id": "room_44b1",
    "guest_id": "guest_1a2b3c",
    "check_in": "2026-09-14",
    "check_out": "2026-09-17",
    "guests": 2
  }'
```

Without a `payment_method_id`, the reservation is created in `pending` status and
must be confirmed separately. Supply a `payment_method_id` to attempt an
authorization immediately — on success, the reservation moves straight to `confirmed`.

## Next steps

- [Authentication](authentication.md) — API keys vs. OAuth2 for partner apps
- [Webhooks](webhooks.md) — get notified when a reservation's status changes
- [Errors](errors.md) — the shape of error responses and common error codes
- [Pagination](pagination.md) — paging through list endpoints

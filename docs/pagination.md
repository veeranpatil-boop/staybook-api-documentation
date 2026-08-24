# Pagination

List endpoints (`GET /properties`, `GET /properties/{id}/rooms`, `GET /reservations`)
use cursor-based pagination rather than page numbers, so results stay consistent
even if records are added or removed between requests.

## Requesting a page

- `limit` — number of items to return (1-100, default 20)
- `cursor` — an opaque token from a previous response's `next_cursor`. Omit it to
  fetch the first page.

```bash
curl "https://api.staybook.dev/v1/reservations?limit=50" \
  -H "X-API-Key: sk_live_xxx"
```

```json
{
  "data": [ /* up to 50 reservations */ ],
  "next_cursor": "eyJpZCI6InJlc182N2FiIn0"
}
```

## Fetching the next page

Pass the returned `next_cursor` back as the `cursor` parameter:

```bash
curl "https://api.staybook.dev/v1/reservations?limit=50&cursor=eyJpZCI6InJlc182N2FiIn0" \
  -H "X-API-Key: sk_live_xxx"
```

When `next_cursor` is `null`, you've reached the last page.

## Why cursors instead of page numbers?

Page-number pagination (`?page=3`) can skip or repeat records if the underlying
data changes between requests — a new reservation on page 1 pushes everything else
down by one, and what used to be the last item on page 2 quietly reappears as the
first item on page 3. Cursors anchor to a specific record instead of a position, so
that can't happen.

**Don't** construct or modify cursor values yourself — treat them as opaque strings.
Their format may change without notice.

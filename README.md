# StayBook API Documentation

Sample API documentation portfolio project — a fictional hotel booking/reservations
API, documented end to end: OpenAPI reference, conceptual guides, and an automated
publishing pipeline.

**This is a documentation writing sample.** The API itself doesn't exist; there's no
live server behind these examples.

## What's here

| Path | Contents |
|---|---|
| [`openapi/openapi.yaml`](openapi/openapi.yaml) | Full OpenAPI 3.1 spec — 7 resources, two auth schemes, cursor pagination, structured errors |
| [`docs/getting-started.md`](docs/getting-started.md) | First integration, start to finish |
| [`docs/authentication.md`](docs/authentication.md) | API keys vs. OAuth2 client credentials |
| [`docs/pagination.md`](docs/pagination.md) | Cursor-based pagination |
| [`docs/errors.md`](docs/errors.md) | Error shape and common error codes |
| [`docs/rate-limiting.md`](docs/rate-limiting.md) | Limits, headers, backoff |
| [`docs/webhooks.md`](docs/webhooks.md) | Event subscriptions, signature verification, retries |

## Why this project

Most sample API docs are just an OpenAPI file. This one is built the way a real
developer-docs site is: reference documentation generated from the spec, sitting
alongside hand-written conceptual guides that a spec can't express on its own —
*why* cursors instead of page numbers, *how* to verify a webhook signature, *what*
to do when a request times out and you're not sure whether it succeeded.

## Design decisions worth noting

- **Cursor pagination over offset/page numbers** — documented with the reasoning,
  not just the mechanics, in [`docs/pagination.md`](docs/pagination.md).
- **Two auth schemes** (API key + OAuth2 client credentials) to reflect a real
  split between direct integrators and partner platforms.
- **A `409 Conflict` on booking creation** — modeling the real-world race condition
  where two people try to book the last room at once, and documenting it as an
  expected, handleable response rather than an edge case.
- **Webhook signature verification with sample code**, because "verify the
  signature" without a snippet is advice nobody follows.

## Building the docs locally

```bash
npx @redocly/cli lint openapi/openapi.yaml       # validate the spec
npx @redocly/cli build-docs openapi/openapi.yaml  # generate static reference HTML
```

Pushing to `main` runs the same lint step in CI, then builds and publishes the
reference docs to GitHub Pages via `.github/workflows/publish.yml`.

## Related sample

A second sample in this portfolio, [my-api-docs](https://github.com/veeranpatil-boop/my-api-docs),
documents a simpler task-management API and shows a ReadMe.io-based publishing
pipeline instead of a static-site one.

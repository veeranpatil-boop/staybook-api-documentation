<img width="1407" height="607" alt="image" src="https://github.com/user-attachments/assets/a60d462a-f592-4a7e-a506-280d5ccf8a90" /># StayBook API Documentation

Sample API documentation portfolio project for a fictional hotel booking and reservations API. The project includes end-to-end documentation, including an OpenAPI reference, conceptual guides, and an automated publishing pipeline.

**This is a documentation writing sample.** The API is fictional, and there is no live server behind the examples.

## What's here

| Path | Contents |
|---|---|
| [`openapi.yaml`](../openapi/openapi.yaml) | Full OpenAPI 3.1 spec — 7 resources, two auth schemes, cursor pagination, structured errors |
| [`getting-started.md`](getting-started.md) | First integration, start to finish |
| [`authentication.md`](authentication.md) | API keys vs. OAuth2 client credentials |
| [`pagination.md`](pagination.md) | Cursor-based pagination |
| [`errors.md`](errors.md) | Error shape and common error codes |
| [`rate-limiting.md`](rate-limiting.md) | Limits, headers, backoff |
| [`webhooks.md`](webhooks.md) | Event subscriptions, signature verification, retries |

## Why this project

Most sample API documentation includes only an OpenAPI file. This project is structured more like a real developer documentation site.

The API reference is generated from the OpenAPI specification and is supported by hand-written conceptual guides that explain information a specification alone cannot cover, such as:
- Why the API uses cursors instead of page numbers.
- How to verify a webhook signature.
- What to do when a request times out and you're not sure whether it succeeded.


## Design decisions worth noting

- **Cursor pagination instead of offset or page numbers** - The documentation explains the reasoning behind this approach, not just how to use it.
- **Two authentication methods** - API keys for direct integrations and OAuth 2.0 client credentials for partner applications.
- **A `409 Conflict` on booking creation** - Models the real-world situation where two people try to book the last available room at the same time. The documentation treats this as an expected error that developers can handle, rather than an edge case.
- **Webhook signature verification with sample code** - Includes a practical example because instructions to "verify the signature" are much more useful when developers can see how to implement it.
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

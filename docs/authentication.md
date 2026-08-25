# Authentication

StayBook offers two authentication methods. The one you use depends on what you're building.

| Method | Use when | Header/flow |
|---|---|---|
| API key | Your own server connects directly to a single StayBook account | `X-API-Key` header |
| OAuth2 client credentials | You're a partner platform acting on behalf of multiple properties | Bearer token from `/oauth/token` |

## API keys

Generate a key per environment from **Dashboard → Developers → API Keys**. Keys are
scoped to a single environment — a sandbox key only works against
`sandbox.api.staybook.dev`, and a production key only works against
`api.staybook.dev`.

Send the key on every request:

```bash
curl https://api.staybook.dev/v1/properties \
  -H "X-API-Key: sk_live_xxx"
```

Treat API keys like passwords. Don't commit them to source control or expose them in client-side code. A leaked key can give someone full access to the account's reservations and guest data.

## OAuth2 client credentials

Partner applications that work with multiple StayBook properties use OAuth 2.0 client credentials instead of a single API key.

1. Register your application to get a `client_id` and `client_secret`.
2. Use them for an access token:

   ```bash
   curl -X POST https://auth.staybook.dev/oauth/token \
     -d grant_type=client_credentials \
     -d client_id=YOUR_CLIENT_ID \
     -d client_secret=YOUR_CLIENT_SECRET \
     -d scope="reservations:read reservations:write"
   ```

3. Use the returned token as a bearer token:

   ```bash
   curl https://api.staybook.dev/v1/reservations \
     -H "Authorization: Bearer ACCESS_TOKEN"
   ```

Access tokens expire after 1 hour. Request a new token when you receive a `401` error with the `token_expired` code. Don't cache tokens indefinitely.

### Scopes

| Scope | Grants |
|---|---|
| `reservations:read` | List and retrieve reservations |
| `reservations:write` | Create, modify, and cancel reservations |

*Note* Request only the scopes your integration needs.

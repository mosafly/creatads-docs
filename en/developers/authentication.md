# Authentication

## API Keys

CreatAds uses API keys for all developer access. Keys:
- Start with `cads_` followed by 64 hex characters
- Are linked to your user account (not a client workspace)
- Require an active premium subscription to use
- Can be revoked at any time from the dashboard or CLI

### Generate a key

1. Go to **Settings → API** in the CreatAds dashboard
2. Click **New API Key**, give it a name (e.g. "Production", "Claude Code")
3. Copy the key — **it's only shown once**

Or via CLI (requires being logged in):
```bash
creatads keys create --name "My integration"
```

### Use a key

Pass the key in the `X-Api-Key` header:

```bash
curl -X POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: cads_your_key_here" \
  -d '{"action": "list_clients"}'
```

The SDK and CLI handle this automatically once configured.

## Security

**Never commit your API key.** Use environment variables:

```bash
# .env
CREATADS_API_KEY=cads_...
```

```typescript
import { CreatadsClient } from "@creatads/sdk";
const client = new CreatadsClient(process.env.CREATADS_API_KEY!);
```

**Rotate keys regularly.** If a key is compromised:
```bash
creatads keys list          # find the key ID
creatads keys revoke <id>   # revoke immediately
creatads keys create --name "Replacement"
```

## Errors

| HTTP status | Error code | Meaning |
|---|---|---|
| 401 | `invalid_api_key` | Key not found, revoked, or malformed |
| 403 | `premium_required` | No active subscription |
| 403 | `forbidden` | Resource doesn't belong to your account |
| 400 | `missing_param` | Required parameter not provided |
| 404 | `not_found` | Resource doesn't exist |
| 500 | — | Server error (retry) |

## Two auth paths

The `/api` edge function supports two authentication modes:

| Header | Actions | Use case |
|---|---|---|
| `X-Api-Key: cads_...` | All business actions (generate, list, create) | Normal API usage |
| `Authorization: Bearer <jwt>` | Key management only (create/list/revoke keys) | Dashboard only |

Key management via `Authorization` requires a valid Supabase JWT (session token from the web app). This is intentional — you can't create new API keys using an existing API key.

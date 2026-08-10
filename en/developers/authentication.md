# Authentication

CreatAds uses two separate authentication paths.

| Credential | Header | Allowed operations |
|---|---|---|
| CreatAds API key | `X-Api-Key: cads_...` | Workspaces, campaigns, creatives, angles and Brand Kit |
| Supabase session JWT | `Authorization: Bearer <jwt>` | Create, list or revoke API keys |

## CreatAds API keys

An API key starts with `cads_` followed by 64 hexadecimal characters. It belongs to the user account, not to one workspace, and is shown only once when created.

Create and revoke keys from **Settings > API**. Store them in a secret manager or environment variable:

```bash
CREATADS_API_KEY=cads_...
```

```bash
curl -X POST "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api" \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $CREATADS_API_KEY" \
  -d '{"action":"list_clients"}'
```

Never commit a key. Revoke it immediately if it is exposed.

## Key management from the CLI or SDK

Key-management operations cannot be authorized with an existing CreatAds key. They require the current Supabase session JWT.

```bash
creatads keys list --jwt <supabase-session-jwt>
creatads keys create --name "Production" --jwt <supabase-session-jwt>
creatads keys revoke <key-id> --jwt <supabase-session-jwt>
```

Without `--jwt`, the CLI prompts for it. For normal use, the dashboard is the simpler key-management path.

## Common responses

| HTTP | Code | Meaning |
|---:|---|---|
| 401 | `invalid_api_key` | Missing, malformed, unknown or revoked key |
| 403 | `premium_required` | The account does not have API access |
| 403 | `forbidden` | The resource does not belong to the account |
| 403 | `quota_exceeded` | The requested batch exceeds the remaining credits |
| 400 | `missing_param` | Required field missing |
| 404 | `not_found` | Resource not found |

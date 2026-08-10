# CreatAds developer platform

> Contract verified against the application code on 2026-07-10.

CreatAds exposes campaign generation through one action-based REST endpoint and three clients built on top of it.

| Interface | Current status | Best for |
|---|---|---|
| REST API | Production endpoint | Any HTTP integration |
| TypeScript SDK | Private monorepo package | Internal Node.js integrations |
| CLI | Monorepo package | Terminal workflows and scripts |
| Remote MCP | `https://api.creatads.co/mcp` | MCP-compatible agents |
| Local MCP | stdio monorepo package | Local agents using CLI credentials |

The REST API, SDK, CLI and MCP cover workspaces, campaigns, creatives, angles and the Brand Kit. Inspiration, Explorer, Ads Library search, Clone and the full Mode Pro preset matrix are application-only today.

## Requirements

- a CreatAds account at [creatads.co](https://creatads.co);
- an API key created in **Settings > API**;
- a plan that includes developer access. The public offering documents this for Founder, Growth and Agency.

Implementation note: the REST premium gate currently accepts every active paid Stripe product, including Solo, while the UI hides API-key access from Solo. Conversely, the UI feature table includes Beta, but the REST gate does not recognize the beta role as a paid subscription. This is an unresolved product/code policy mismatch.

Generation credits are shared with the web application: one generated image consumes one credit.

| Plan | Monthly image credits |
|---|---:|
| Starter | 3 |
| Beta | 15 while beta access is active |
| Solo | 30 |
| Founder | 80 |
| Growth | 350 |
| Agency | Unlimited |

## Quick start

```bash
creatads auth login
creatads clients list
creatads clients use <client-id>
creatads angles generate \
  --summary "Premium skincare brand for urban women aged 25-40" \
  --language en
creatads campaigns create \
  --name "Summer Sale" \
  --cta "Shop now" \
  --offer "20% off this weekend" \
  --aspect-ratio "1:1,9:16" \
  --volume 4
creatads campaigns generate <campaign-id>
```

## API topology

```text
REST / SDK / CLI / MCP
          |
          v
POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
          |
          +-- Supabase: authentication, data, billing and quota checks
          +-- api.creatads.co: copy, angle and image processing
```

## Documentation

- [Authentication](./authentication.md)
- [REST API](./api.md)
- [CLI](./cli.md)
- [TypeScript SDK](./sdk.md)
- [MCP](./mcp.md)
- [Workflows and limitations](./workflows.md)

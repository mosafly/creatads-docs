# CreatAds — Developer Platform

> Generate AI ad creatives programmatically — from your terminal, your code, or your AI agent.

## What is the CreatAds Developer Platform?

CreatAds exposes its full generation pipeline through three interfaces:

| Interface | Best for |
|---|---|
| **CLI** (`creatads-cli`) | Terminal workflows, scripts, CI/CD |
| **TypeScript SDK** (`@creatads/sdk`) | Node.js apps, custom integrations |
| **MCP Server** (`creatads-mcp`) | Claude Code, AI agents, LLM workflows |

All three share the same authentication (API key) and the same underlying REST API.

## Prerequisites

- A CreatAds account at [creatads.co](https://creatads.co)
- An **active subscription** (Founder, Growth, or Agency) — the developer platform is premium-only
- An API key — generate one in **Settings → API**

## Quickstart (5 minutes)

### 1. Install the CLI

```bash
npm install -g creatads-cli
```

### 2. Authenticate

```bash
creatads auth login
# Paste your API key when prompted (starts with cads_...)
```

### 3. Set your default client

```bash
creatads clients list
creatads clients use <client-id>
```

### 4. Generate your first campaign

```bash
# Generate audience profiles from your brand description
creatads angles generate \
  --summary "French e-commerce brand selling eco-friendly home products to urban millennials" \
  --language fr

# Create a campaign
creatads campaigns create \
  --name "Summer Sale 2026" \
  --cta "Découvrir" \
  --offer "-20% ce weekend" \
  --aspect-ratio "1:1,9:16" \
  --volume 6

# Generate creatives (waits ~3 min, shows spinner)
creatads campaigns generate <campaign-id>
```

## Architecture

```
Your code / agent
       │
       ▼
┌─────────────────┐
│  REST API        │  POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
│  X-Api-Key auth  │
└────────┬────────┘
         │
    ┌────┴────────────────────┐
    │                         │
    ▼                         ▼
Supabase Edge Functions   imgproc Bun Service
(auth, DB, billing)       api.creatads.co
                          (AI generation: fal.ai, OpenRouter)
```

## Sections

- [Authentication](./authentication.md) — API keys, security
- [CLI Reference](./cli.md) — All commands with examples
- [TypeScript SDK](./sdk.md) — `@creatads/sdk` reference
- [MCP Server](./mcp.md) — Claude Code & AI agent integration
- [Workflows](./workflows.md) — Real-world examples

## Rate limits & quotas

Generation quotas are shared with the web app — the same monthly limit applies regardless of how you generate.

| Plan | Generations/month |
|---|---|
| Founder | 60 |
| Growth | 250 |
| Agency | Unlimited |

API requests (list, create, etc.) are not counted against your quota — only generation calls.

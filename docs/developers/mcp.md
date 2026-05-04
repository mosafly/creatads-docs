# MCP Server — Claude Code & AI Agents

The `creatads-mcp` server exposes the CreatAds API as MCP tools, allowing Claude Code and any MCP-compatible agent to generate ad creatives directly from a conversation.

## Setup

### 1. Install & configure the CLI first

```bash
npm install -g creatads-cli
creatads auth login
creatads clients use <your-client-id>
```

The MCP server reads `~/.creatads/config.json` — the same config as the CLI.

### 2. Register in Claude Code

Add to `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "creatads": {
      "command": "node",
      "args": ["/path/to/creatads/services/mcp/dist/index.js"]
    }
  }
}
```

Or if published on npm (`creatads-mcp`):

```json
{
  "mcpServers": {
    "creatads": {
      "command": "npx",
      "args": ["creatads-mcp"]
    }
  }
}
```

### 3. Verify

In a Claude Code session:
```
Use the creatads list_clients tool
```

---

## Available tools

### `list_clients`
List all client workspaces.
```
No parameters required.
```

### `create_client`
Create a new client workspace for a brand.
```
name (string, required) — Brand name
```

### `list_campaigns`
List all campaigns for a client.
```
client_id (string, required)
```

### `create_campaign`
Create a new campaign with a brief.
```
client_id        (string, required)
name             (string, required)
cta_text         (string) — e.g. "Découvrir", "Shop now"
offer_text       (string) — e.g. "-20% ce weekend"
aspect_ratio     (string) — comma-separated: "1:1", "4:5", "9:16", "1.91:1"
volume           (number) — total creatives to generate (1–60)
selected_profile_ids  (string[]) — profile IDs from list_profiles
landing_url      (string)
```

### `generate_creatives`
Generate AI visuals for a campaign. Polls automatically until done.
```
campaign_id  (string, required)
wait         (boolean) — true = wait for results (default), false = fire-and-forget
```
Returns array of creatives with `image_url` when `wait=true`.

### `list_creatives`
List generated creatives for a campaign.
```
campaign_id (string, required)
```

### `list_profiles`
List audience personas for a client.
```
client_id (string, required)
```

### `get_profile`
Get full detail of a single profile.
```
profile_id (string, required)
```

### `generate_profiles`
Generate 10 AI audience personas from a brand description.
```
client_id         (string, required)
research_summary  (string, required) — brand/product description
language          (string) — "fr" (default) or "en"
```

### `get_brand_kit`
Get the brand kit (colors, name, description) for a client.
```
client_id (string, required)
```

---

## Example agent workflow

Here is a complete prompt for Claude Code to generate a full campaign:

```
I need to launch a campaign for my client Bobotcho (bidet brand).
Use the creatads tools to:
1. List the existing profiles and pick the 2 most relevant for urban eco-conscious buyers
2. Create a campaign called "Été 2026" with CTA "Découvrir", offer "Livraison offerte", 
   formats 1:1 and 9:16, volume 4
3. Generate the creatives and return the image URLs
```

Claude will autonomously call `list_profiles` → `create_campaign` → `generate_creatives` and return the URLs.

---

## Typical call sequence

```
1. list_clients                          → get client_id
2. list_profiles(client_id)              → get profile IDs
   └─ or generate_profiles(...)         → if no profiles yet
3. create_campaign(client_id, ...)       → get campaign_id
4. generate_creatives(campaign_id)       → wait=true, get image_url[]
```

---

## Error messages

The MCP server returns human-readable errors as text content:

| Situation | Message |
|---|---|
| Not configured | `Not configured. Run: creatads auth login` |
| Invalid key | `Invalid or revoked API key. Run: creatads auth login` |
| No subscription | `Premium subscription required. Upgrade at https://creatads.app/pricing` |
| Missing param | `Missing required parameter: <name>` |

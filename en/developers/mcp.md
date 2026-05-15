# MCP Server — Claude Code & AI Agents

The CreatAds MCP server exposes the full API as tools, letting Claude Code and any MCP-compatible agent generate ad creatives directly from a conversation — no UI needed.

The server runs at `https://api.creatads.co/mcp` (HTTP, stateless JSON-RPC 2.0). No local install required.

## Setup

### 1. Get your API key

Open **Settings → API Keys** in the app and copy your `cads_...` key.

### 2. Add the MCP server to Claude Code

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

That's it. Claude Code will discover the tools automatically.

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
selected_angle_ids  (string[]) — profile IDs from list_angles
landing_url      (string)
```

### `generate_creatives`
Trigger AI generation for a campaign (fire-and-forget).
```
campaign_id  (string, required)
```
Generation runs in the background. Call `list_creatives` to check progress.

### `list_creatives`
List generated creatives for a campaign.
```
campaign_id (string, required)
```

### `list_angles`
List creative angles for a client.
```
client_id (string, required)
```

### `get_angle`
Get full detail of a single profile.
```
angle_id (string, required)
```

### `generate_angles`
Generate 10 Andromeda-native creative angles from a brand description.
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

Paste this prompt in Claude Code to generate a full campaign autonomously:

```
I need to launch a campaign for my client Bobotcho (bidet brand).
Use the creatads tools to:
1. List the existing profiles and pick the 2 most relevant for urban eco-conscious buyers
2. Create a campaign called "Été 2026" with CTA "Découvrir", offer "Livraison offerte",
   formats 1:1 and 9:16, volume 4
3. Generate the creatives, then poll list_creatives until images are ready and return the URLs
```

Claude will autonomously call `list_angles` → `create_campaign` → `generate_creatives` → `list_creatives` and return the URLs.

---

## Typical call sequence

```
1. list_clients                          → get client_id
2. list_angles(client_id)              → get profile IDs
   └─ or generate_angles(...)         → if no profiles yet
3. create_campaign(client_id, ...)       → get campaign_id
4. generate_creatives(campaign_id)       → starts background generation
5. list_creatives(campaign_id)           → poll until images appear
```

---

## Error messages

| Situation | Message |
|---|---|
| Missing or invalid key | `Invalid or revoked API key` |
| No subscription | `Premium subscription required. Upgrade at https://creatads.co/pricing` |
| Missing param | `Missing required parameter: <name>` |

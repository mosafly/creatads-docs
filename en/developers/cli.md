# CLI Reference

## Installation

```bash
npm install -g creatads-cli
```

## Global options

```bash
creatads --version   # 1.0.0
creatads --help
creatads <command> --help
```

---

## auth

Manage authentication credentials stored in `~/.creatads/config.json`.

### `auth login`
Configure your API key interactively.
```bash
creatads auth login
# Paste your API key (cads_...) when prompted
```

### `auth whoami`
Show the currently configured key prefix and API URL.
```bash
creatads auth whoami
# Base URL: https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
# Key prefix: cads_2ad912d…
```

### `auth logout`
Remove stored credentials.
```bash
creatads auth logout
```

---

## clients

Manage client workspaces. Each client is a brand or advertiser.

### `clients list`
```bash
creatads clients list
# ┌──────────────────────────────────────┬──────────┬─────────┬─────────────┐
# │ ID                                   │ Name     │ Default │ Created     │
# ├──────────────────────────────────────┼──────────┼─────────┼─────────────┤
# │ 89b18b5f-b3a8-4cf6-9b47-beafb9956b04 │ Bobotcho │ ✓       │ 4 mars 2026 │
# └──────────────────────────────────────┴──────────┴─────────┴─────────────┘
```

### `clients create <name>`
```bash
creatads clients create "Nike France"
```

### `clients use <id>`
Set a default client — avoids passing `--client` to every subsequent command.
```bash
creatads clients use 89b18b5f-b3a8-4cf6-9b47-beafb9956b04
# Default client set to: Bobotcho (89b18b5f-...)
```

After this, all `--client` options become optional.

---

## campaigns

### `campaigns list`
```bash
creatads campaigns list
creatads campaigns list --client <id>   # override default

# ┌──────────────────────────────────────┬────────────┬────────────┬────────┬────────────┐
# │ ID                                   │ Name       │ Status     │ Volume │ Created    │
# └──────────────────────────────────────┴────────────┴────────────┴────────┴────────────┘
```

### `campaigns create`
```bash
creatads campaigns create \
  --name "Summer Sale" \
  --cta "Découvrir" \
  --offer "-20% ce weekend" \
  --aspect-ratio "1:1,9:16" \
  --volume 6 \
  --landing-url "https://example.com/sale"

# Options:
#   --client <id>          Client ID (uses default if set)
#   --name <n>             Campaign name (required)
#   --cta <text>           CTA button text
#   --offer <text>         Offer/promo text
#   --aspect-ratio <ratio> Comma-separated: 1:1, 4:5, 9:16, 1.91:1 (default: 1:1)
#   --volume <n>           Total creatives to generate (default: 1)
#   --landing-url <url>    Destination URL
```

### `campaigns generate <campaign-id>`
Launch generation and wait for results (~2–5 min).
```bash
creatads campaigns generate 03d75c13-a9a2-40a0-88b4-acf7a2e702f9

# ⠸ Generating creatives…
# ✔ Done — 3 creative(s) generated
#
# ┌──────────────────────────────────────┬───────────────┬──────────────┬────────────┐
# │ ID                                   │ Image URL     │ Aspect Ratio │ Created    │
# └──────────────────────────────────────┴───────────────┴──────────────┴────────────┘
```

**Options:**
```bash
--no-wait           Fire-and-forget (returns job_id immediately)
--timeout <seconds> Generation timeout in seconds (default: 180)
```

---

## creatives

### `creatives list <campaign-id>`
```bash
creatads creatives list 03d75c13-a9a2-40a0-88b4-acf7a2e702f9
```

---

## profiles

Creative angles used to drive campaign diversity.

### `profiles list`
```bash
creatads angles list
creatads angles list --client <id>
```

### `profiles get <id>`
Show full detail of a single profile.
```bash
creatads angles get c4764793-527f-4afc-8126-e0a3a34cfe7d

# Seniors (65+) & Retirees in Abidjan
# ────────────────────────────────────────────────────────────
# ID          c4764793-527f-4afc-8126-e0a3a34cfe7d
# Client ID   89b18b5f-b3a8-4cf6-9b47-beafb9956b04
# Créé le     4 mars 2026
#
# Pain Point
#   Loss of dignity and fear of being a burden...
#
# Angle
#   The "Dignity & Independence" approach: Focus on maintaining privacy...
```

### `profiles generate`
Generate 10 AI creative angles from a brand/product description.
```bash
creatads angles generate \
  --summary "French DTC brand selling minimalist leather wallets to urban professionals aged 25-40, focusing on quality and sustainability" \
  --language fr

# Options:
#   --client <id>        Client ID (uses default if set)
#   --summary <text>     Brand/product description (required, richer = better profiles)
#   --language <lang>    fr (default) or en
```

---

## keys

Manage API keys for your account.

### `keys list`
```bash
creatads keys list
```

### `keys create`
```bash
creatads keys create --name "Production server"
# ✔ Key created: cads_xxxx… (copy now — shown only once)
```

### `keys revoke <id>`
```bash
creatads keys revoke <key-id>
```

---

## Configuration file

Stored at `~/.creatads/config.json` (chmod 600):

```json
{
  "api_key": "cads_...",
  "base_url": "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api",
  "default_client_id": "89b18b5f-b3a8-4cf6-9b47-beafb9956b04"
}
```

You can edit this file directly to point to a different API endpoint (e.g. local dev).

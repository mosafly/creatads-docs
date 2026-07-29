# CLI reference

The CLI package is `creatads-cli` version 1.0.0. Its source lives in `services/cli`. Build it from the monorepo, or install it from a registry only when a published package is available.

```bash
creatads --help
creatads --version
```

## Authentication

```bash
creatads auth login
creatads auth whoami
creatads auth logout
```

`auth login` stores the API key and base URL in `~/.creatads/config.json`. Never commit this file.

## Workspaces

```bash
creatads clients list
creatads clients create "Maison Leon"
creatads clients use <client-id>
```

The default client makes `--client <id>` optional in later commands.

## Angles

```bash
creatads angles list [--client <id>]
creatads angles get <angle-id>
creatads angles generate \
  [--client <id>] \
  --summary "Brand, product, audience, benefits and market" \
  [--language fr|en]
```

`angles generate` creates ten angles. The former `profiles` commands no longer exist.

## Campaigns

```bash
creatads campaigns list [--client <id>]

creatads campaigns create \
  [--client <id>] \
  --name "Summer Sale" \
  [--cta "Shop now"] \
  [--offer "20% off"] \
  [--aspect-ratio "1:1,9:16"] \
  [--volume 4] \
  [--landing-url "https://example.com"]

creatads campaigns generate <campaign-id> [--no-wait] [--timeout 180]
```

Without `--no-wait`, the CLI polls until at least one creative is returned or the timeout is reached. This is not a guarantee that every expected image in a multi-image batch is ready; use `creatads creatives list` to verify the final count.

The CLI campaign command does not currently expose `selected_angle_ids`, image URLs or platform targets even though the REST API and SDK accept them.

## Creatives

```bash
creatads creatives list <campaign-id>
```

## API keys

```bash
creatads keys list [--jwt <supabase-session-jwt>]
creatads keys create [--name "Production"] [--jwt <supabase-session-jwt>]
creatads keys revoke <key-id> [--jwt <supabase-session-jwt>]
```

These commands require a Supabase session JWT. If omitted, the CLI prompts for it.

## Local configuration

```json
{
  "api_key": "cads_...",
  "base_url": "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api",
  "default_client_id": "uuid"
}
```

# MCP integration

CreatAds provides two MCP transports with slightly different contracts.

## Remote HTTP MCP

Endpoint: `https://api.creatads.co/mcp`

Authenticate with the CreatAds API key as a bearer token:

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

The remote server exposes eleven tools:

- `list_clients`
- `create_client`
- `upload_image`
- `list_campaigns`
- `create_campaign`
- `generate_creatives`
- `list_creatives`
- `list_angles`
- `get_angle`
- `generate_angles`
- `get_brand_kit`

Treat live MCP discovery as authoritative if the deployed service differs. `generate_creatives` returns immediately in HTTP mode. Poll `list_creatives` every 15 seconds until the expected count appears.

`upload_image` accepts a base64-encoded JPEG, PNG or WebP and returns a public URL that can be passed as `reference_image_url` or `product_image_url` to `create_campaign`.

## Local stdio MCP

The local package is `creatads-mcp` in `services/mcp`. It reads `~/.creatads/config.json`, so run `creatads auth login` first.

It exposes ten business tools: the same set as above except `upload_image`. Its `generate_creatives` tool accepts an optional `wait` flag and waits by default, but the shared SDK poller returns on the first available creative rather than the complete expected batch.

## Example agent workflow

```text
1. list_clients
2. list_angles or generate_angles
3. upload_image (remote only, when local images are needed)
4. create_campaign with selected_angle_ids
5. generate_creatives
6. list_creatives until all expected images are present
```

## Scope

MCP does not expose Inspiration, Explorer, Ads Library search, Clone, favorites, winners or the full Mode Pro modes/presets matrix.

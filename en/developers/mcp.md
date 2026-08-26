# MCP integration

Remote HTTP endpoint: `https://api.creatads.co/mcp`

> This is the V2 contract. It becomes available once the
> `mcp_generation_batches` migration has been applied and the imgproc service
> has been deployed. Always use `tools/list` as the source of truth for a live instance.

## Connection

Authenticate the client with a CreatAds API key in the Bearer header.

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

For Codex, configure the same HTTP endpoint with an environment variable that
holds the key; never put the key in the repository.

## Recommended V2 workflow

```text
1. get_generation_capabilities
2. list_clients, get_brand_kit, list_angles and list_assets
3. upload_image or ingest_external_image when needed
4. preview_easy_generation or preview_pro_batch
5. review total_images and estimated_credits
6. call generate_* with confirm: true and a unique idempotency_key
7. poll get_batch_status; use resume_batch or cancel_batch when needed
```

Previews never consume credits. A completed image consumes one credit, so an
explicit confirmation is required before execution.

## V2 tools

- Discovery: `get_generation_capabilities`, `list_creative_modes`,
  `list_formats`, `list_presets`.
- Context: existing `list_clients`, `create_client`, `get_brand_kit`,
  `list_angles`, `get_angle`, `generate_angles`.
- Assets: `upload_image`, `ingest_external_image`, `list_assets`,
  `list_templates`.
- Easy Mode: `preview_easy_generation`, `generate_easy_generation`.
- Pro Mode: `preview_pro_batch`, `generate_pro_batch`, `get_batch_status`,
  `resume_batch`, `cancel_batch`.

Easy Mode accepts one to three variants, or one slot per selected preset. Pro
Mode computes the real Cartesian product
`ratios × modes/scenes × angles × repetitions`, capped at 60 images. Selecting
a preset neutralizes repetitions, matching the application.

`cancel_batch` cancels slots that have not started. An image already running at
the provider may still complete, but it will not restart the batch.

## V1 compatibility

The eleven legacy tools, including `create_campaign` and `generate_creatives`,
remain temporarily available for existing integrations. They use the former
campaign model and must not be used for new work: they do not cover Easy Mode,
presets, cost preview, or durable V2 batches.

## Current exclusions

Ads Library exploration and Meta publishing are not exposed through V2. Meta
publishing remains disabled in the product during the Analytics phase.

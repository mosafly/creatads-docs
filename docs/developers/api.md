# REST API reference

All REST operations use a single action-based endpoint:

```text
POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
```

Send `Content-Type: application/json`, a valid `X-Api-Key` header and an `action` in the JSON body.

```bash
curl -X POST "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api" \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $CREATADS_API_KEY" \
  -d '{"action":"list_clients"}'
```

## Available actions

| Action | Required fields | Result |
|---|---|---|
| `list_clients` | none | `{ clients }` |
| `create_client` | `name` | `{ client }` |
| `list_campaigns` | `client_id` | `{ campaigns }` |
| `create_campaign` | `client_id` | `{ campaign }` |
| `list_creatives` | `campaign_id` | `{ creatives }` |
| `list_angles` | `client_id` | `{ angles }` |
| `get_angle` | `angle_id` | `{ angle }` |
| `generate_angles` | `client_id`, `research_summary` | `{ angles }` |
| `get_brand_kit` | `client_id` | `{ brand_kit }` |
| `prepare_campaign` | `campaign_id` | Prepared copy and recommendations |
| `generate_creatives` | `campaign_id` | Pending job metadata |

JWT-only actions are `list_api_keys`, `create_api_key` and `revoke_api_key`.

## Create a campaign

```json
{
  "action": "create_campaign",
  "client_id": "uuid",
  "name": "Summer Sale",
  "cta_text": "Shop now",
  "offer_text": "20% off this weekend",
  "aspect_ratio": "1:1,9:16",
  "volume": 4,
  "selected_angle_ids": ["angle-uuid"],
  "platform_target": ["facebook", "instagram"],
  "landing_url": "https://example.com/sale",
  "reference_image_url": "https://example.com/reference.jpg",
  "product_image_url": "https://example.com/product.png"
}
```

Only `client_id` is required. Defaults are: `Untitled Campaign`, ratio `1:1`, volume `1`, empty text and angle/platform lists, and no image URLs. `use_brand_kit` is forced to `true` by this action.

## Generate and poll

```json
{
  "action": "generate_creatives",
  "campaign_id": "uuid"
}
```

```json
{
  "job_id": "uuid",
  "campaign_id": "uuid",
  "status": "pending",
  "jobs": 4
}
```

Generation runs in the background. Poll `list_creatives` until the expected image count is available. Each successfully generated image consumes one credit; the whole batch is checked against the remaining quota before dispatch.

The effective job count is `combinations x max(1, floor(volume / combinations))`, where combinations are selected angles (or one empty angle) times ratios. A non-divisible volume is rounded down; a volume smaller than the combinations produces one job per combination and can be exceeded. Use the response's `jobs` field as the effective batch size.

The API does not expose the complete Mode Pro modes/presets matrix available in the application.

## Generate angles

```json
{
  "action": "generate_angles",
  "client_id": "uuid",
  "research_summary": "Premium skincare brand for urban women aged 25-40.",
  "language": "en"
}
```

This creates ten angles with a name, hook, awareness level, visual style and copy direction. Angle generation does not consume an image credit.

## Not exposed

There is currently no public REST action for Inspiration, Explorer, Ads Library search, Clone, favorites or winners. These are application workflows.

For the complete field and error contract, see the repository-level [`API_DOCS.md`](../../API_DOCS.md).

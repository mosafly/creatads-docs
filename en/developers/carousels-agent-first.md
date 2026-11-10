# Agent First carousels: Claude, ChatGPT and Grok

Your agent writes organic copy, saves it in CreatAds, edits slides and retrieves ready-to-publish files without a browser. Instagram/TikTok, editorial/product content, 3–10 slides and `1:1`, `4:5`, `9:16` ratios share one contract across the app, API and MCP.

Availability requires deployment of imgproc and the `api` Edge Function with the `agent_carousels` migration. Check `get_carousel_capabilities` on your instance. The examples describe integrations to configure; they do not mean your accounts are already connected.

## Workflow

1. Call `list_clients`, then read the chosen client's Brand Kit and `list_assets`. Use verified product facts.
2. Write slide titles, bodies, a caption and up to five hashtags in your agent.
3. Call `create_carousel` with a stable `idempotency_key`. The document remains a draft.
4. Optionally use `preview_pro_batch`, one `item` per slide, the carousel ratio, `prompt_override` containing the exact visual brief and the slide's title/body as context that must never be printed, `use_angle: false`, `use_brand_kit: false`. Describe a concrete photographic scene without text, logos or readable interfaces. Server policy selects GPT Image 2.5 Flare at high quality. Supply owned product references as needed. Show the plan/cost, obtain approval, then call `generate_pro_batch` and `get_batch_status`.
5. Read `get_carousel`, attach successful `creative_id` values through `update_carousel` with `expected_version`. Preserve slide IDs when editing/reordering.
6. Inspect using `render_carousel_slide`, then `export_carousel` for the approved version.

Agent-authored copy, edits, rendering and export use **0 CreatAds credits**. Visuals keep the existing charge: one credit per technically successful image. The app's native outline generation remains a separate one-credit operation. Your agent provider's fees are separate.

## Tools and API actions

| Name | Parameters |
|---|---|
| `get_carousel_capabilities` | none |
| `create_carousel` | `client_id`, `idempotency_key`, `content` |
| `list_carousels` | `client_id`, `limit` (default 30), `offset` (default 0) |
| `get_carousel` | `client_id`, `carousel_id` |
| `update_carousel` | `client_id`, `carousel_id`, `expected_version`, complete `content` |
| `render_carousel_slide` | `client_id`, `carousel_id`, `expected_version`, `slide_id` |
| `export_carousel` | `client_id`, `carousel_id`, `expected_version` |

Create/update return `{ carousel, credits_used: 0 }`. Documents contain `id`, `client_id`, `version`, `status: "draft"`, `content`, `created_at`, `updated_at`. Omitted slide IDs are assigned by the server. `get_carousel` adds `images`, a `slide_id → URL` map of verified images. Lists return `carousels` and `next_offset` or `null`.

Each slide accepts **one** owned, CreatAds-hosted `asset_id` or `creative_id`. Arbitrary image URLs are rejected. Replaying the same creation key/content retrieves the document; changed content under that key raises `idempotency_key_conflict`. Concurrent edits raise `version_conflict` (API HTTP 409). Read and reconcile instead of overwriting.

Exports return `archive`, ordered `slides`, dimensions and `expires_at`. Files are private, with one-hour signed links. ZIPs contain PNGs, `caption.txt`, app-importable `carousel.json`, and `document.json` containing the cloud identity/version. Nothing is published to Instagram or TikTok.

```json
{
  "action": "create_carousel",
  "client_id": "11111111-1111-4111-8111-111111111111",
  "idempotency_key": "coffee-editorial-001",
  "content": {
    "platform": "instagram", "kind": "editorial", "language": "en",
    "ratio": "4:5", "theme": "light", "accent": "#7c3aed", "brand_name": "My cafe",
    "title": "Your coffee ritual", "caption": "What is your ritual?", "hashtags": ["#coffee"],
    "slides": [
      { "title": "Coffee your way", "body": "Start by observing your preparation.", "visual_prompt": "Minimal coffee scene, no typography" },
      { "title": "Note your preferences", "body": "Keep track of the flavors you enjoy.", "visual_prompt": "Coffee cup and blank notebook, no text" },
      { "title": "Share your ritual", "body": "Which step matters most to you?", "visual_prompt": "Hands holding a coffee cup, no text" }
    ]
  }
}
```

Replace the example UUID using `list_clients`. Send the body to the [existing API endpoint](/en/developers/api), with `X-Api-Key`. Its access checks remain applicable. Access generation tools with `{ "action": "call_tool", "name": "preview_pro_batch", "arguments": { ... } }`; MCP owns validation and execution.

## Remote MCP integrations

After deploying the `studio_carousel_documents` migration, the Studio companion can save editable V2 documents in My creations using `list_studio_carousels`, `get_studio_carousel`, and `save_studio_carousel`. Client and generation request IDs deduplicate creation. Edits require the current `expected_version`. My creations opens `/studio?client=…&project=…` in the authenticated application; inference, previews and exports still require the local companion.

`get_studio_carousel` returns `{ document }`. To edit a document at revision `r`, send `expected_version: r + 1` and the updated document with `revision: r + 1`. Preserve its `generation` metadata. The server assigns the creation ID and saves the document, library projection, and revision atomically. Legacy editing, rendering, and export tools return `studio_editor_required` for these documents. The companion exports PNG or ZIP files for the open revision. Gallery thumbnails still use the content projection.

Endpoint: `https://api.creatads.co/mcp`, HTTP transport, CreatAds key in `Authorization: Bearer …`. Stdio forwards this same catalog automatically. Discover schemas with `tools/list`. Retry a timed-out creation with its original key; poll the known batch after a generation timeout.

### Studio conversations and agent

The `20261109130459_studio_conversations.sql` migration adds persistent conversations after `20261109130456_studio_carousel_documents.sql`. On a new instance, deploy both migrations, the backend and the matching frontend before using this flow.

The CreatAds backend and both migrations were deployed on 9 November 2026 ([deployment run](https://github.com/mosafly/creatads/actions/runs/42623190948)). Real-account MCP reads and rejection of requests without a valid session have been verified. The Studio frontend remains available locally/in preview; this connection check does not establish a complete generation flow from a browser session.

| Tool | Parameters | Result |
|---|---|---|
| `get_studio_identity` | `client_id` | authorized `user_id`, `client_id` |
| `list_studio_conversations` | `client_id` | summaries in `conversations`, without messages |
| `get_studio_conversation` | `client_id`, `conversation_id` | full `conversation` |
| `save_studio_conversation` | `client_id`, `document`, optional `expected_version` on creation | saved `conversation` |

Discover the strict schema using `tools/list`. Create with `version: 0` and no `expected_version`; update version `v` with `expected_version: v` and `document.version: v + 1`. Identical retries retrieve the saved document; competing writes return `version_conflict`. Conversations are limited to 100 requests and 2 MiB. These tools persist data without running inference.

The browser calls `POST /studio-agent` using its Supabase session (`Authorization: Bearer …`) and `{ action, ...arguments }`. This route only accepts the seven Studio identity, conversation and document actions. It does not replace key-based MCP authentication. The companion verifies on the server that the browser session and its MCP key belong to the same user and client.

The companion exposes `POST /__agent/turn`, `/cancel`, `/status`, `/preview`, `/export` and `/conversations` on loopback port 8093, with an explicit origin allowlist. A request contains UUIDs `clientId`, `conversationId`, `requestId`, plus `message`, `language`, `platform`, `kind`, `projectId`, `revision`, `slideId` (the last three may be `null`). Reuse the exact request to resume interrupted work. Rerun/Codex selects a validated action, observes its result and continues, up to eight steps and one creation per request. Edits save automatically.

Intents and results persist; private checkpoints avoid repeating completed effects. Stopping a request preserves saved changes. Exports check access again and use the revision in their saved receipt. This personal pilot requires a single companion process per account and is not a distributed runner. Codex requires a ChatGPT account with no API-key fallback. AI photos and logo layers are outside this milestone. See the [Studio agent guide](/en/users/studio-agent).

### Claude API

The [Messages API MCP connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) uses `mcp_servers`, `authorization_token` and an `mcp_toolset`. This configuration targets our Bearer authentication. Select a model available to your account and keep secrets in the environment.

```python
import os
import anthropic
response = anthropic.Anthropic().beta.messages.create(
    model=os.environ["CLAUDE_MODEL"], max_tokens=2000,
    betas=["mcp-client-2025-11-20"],
    mcp_servers=[{"type": "url", "url": "https://api.creatads.co/mcp",
                  "name": "creatads", "authorization_token": os.environ["CREATADS_MCP_TOKEN"]}],
    tools=[{"type": "mcp_toolset", "mcp_server_name": "creatads"}],
    messages=[{"role": "user", "content": "Read carousel capabilities and my workspaces."}],
)
```

### OpenAI Responses API

The [Responses API](https://developers.openai.com/api/docs/guides/tools-connectors-mcp) accepts a remote MCP server and token. OpenAI tool/data-sharing approval and CreatAds image-budget confirmation remain separate controls.

```python
import os
from openai import OpenAI
response = OpenAI().responses.create(
    model=os.environ["OPENAI_MODEL"], input="Read carousel capabilities and my workspaces.",
    tools=[{"type": "mcp", "server_label": "creatads", "server_url": "https://api.creatads.co/mcp",
            "authorization": os.environ["CREATADS_MCP_TOKEN"],
            "allowed_tools": ["get_carousel_capabilities", "list_clients"], "require_approval": "always"}],
)
```

### Grok / xAI API

[xAI remote MCP tools](https://docs.x.ai/developers/tools/remote-mcp) use the same URL and `authorization`. xAI does not support `require_approval`; omit that OpenAI parameter. CreatAds preview/confirmation is still required for paid images.

```python
import os
from openai import OpenAI
client = OpenAI(api_key=os.environ["XAI_API_KEY"], base_url="https://api.x.ai/v1")
response = client.responses.create(
    model=os.environ["GROK_MODEL"], input="Read carousel capabilities and my workspaces.",
    tools=[{"type": "mcp", "server_label": "creatads", "server_url": "https://api.creatads.co/mcp",
            "authorization": os.environ["CREATADS_MCP_TOKEN"],
            "allowed_tools": ["get_carousel_capabilities", "list_clients"]}],
)
```

## ChatGPT: GPT Actions and connectors

For a custom GPT, import `https://api.creatads.co/carousels-openapi.json` into Actions. Configure **API Key → Custom header → X-Api-Key** with your CreatAds key. This schema is generated from the MCP catalog and targets the existing API. See [GPT Actions configuration](https://help.openai.com/en/articles/9442513-configuring-actions-in-gpts) for workspace/domain restrictions.

ChatGPT/Claude web MCP connectors that require OAuth need a dedicated OAuth flow. Our server currently uses Bearer API keys; this release adds no OAuth and connects no accounts automatically. Do not select unauthenticated access to bypass this limitation. API examples and schemas are prepared; each product's end-to-end connection remains to be tested after deployment.

## TypeScript SDK

```ts
const carousel = await client.createCarousel(clientId, content, "stable-request-key");
const current = await client.getCarousel(clientId, carousel.id);
const edited = await client.updateCarousel(clientId, carousel.id, current.carousel.version, revisedContent);
const pack = await client.exportCarousel(clientId, edited.id, edited.version);
// pack.archive.url: signed private ZIP; pack.slides: ordered PNGs.
```

The SDK also exposes `getCarouselCapabilities`, `listCarousels`, `renderCarouselSlide`, and `callTool` for existing generation workflows.

## Monitoring and saved structures

These tools share the app's validation and `client_id` isolation. They require the `carousel_intelligence`, `carousel_intelligence_safety`, `carousel_intelligence_tiktok_budget` and `carousel_watch_followup_activation` migrations, the `api` Edge Function, backend and enabled hosted worker. Discover the live catalog with `tools/list`; check `configuration.workerAlive` before paid work. Access follows the account's Inspiration permissions.

| Action / tool | Main parameters |
|---|---|
| `get_carousel_intelligence` | `client_id`, optional `platform` and `niche`, `limit` 1–100 |
| `create_carousel_watch` | `client_id`, stable UUID `idempotency_key`, `watch` |
| `set_carousel_watch` | `client_id`, `watch_id`, `enabled`, `confirm: true` for activation or budget increases; optional `monthly_budget_usd`, `auto_analyze` |
| `collect_carousel_watch` | `client_id`, `watch_id`, stable UUID `idempotency_key`, `confirm: true` |
| `get_social_carousel` | `client_id`, `post_id` |
| `analyze_social_carousel` | `client_id`, `post_id`, `language`, stable UUID `idempotency_key`, `confirm: true`, recommended `expected_slide_count` |
| `get_carousel_patterns` | `client_id`, optional `platform` and `niche` |
| `get_carousel_pattern` | `client_id`, `analysis_id` |

`watch` contains `platform: instagram|tiktok`, `kind: account|search|post`, `target`, optional `niche`, `intervalHours: 6|12|24`, `monthlyBudgetUsd: 0.15–100` on Instagram or `0.50–100` on TikTok, `autoAnalyze` and `analysisLanguage: fr|en`. Defaults are 24 hours, USD 3, automatic analysis disabled and French. TikTok post targets require a full URL; short share links are not resolved. Creation never starts collection.

Show the budget and obtain human approval before setting `confirm: true`. Collection reserves USD 0.15 on Instagram or USD 0.50 on TikTok, respecting Clockworks' launch minimum. `configuration.collectionReservationByPlatform` exposes both amounts; `max_reservation_usd` follows the watch's platform. Analysis reserves USD 0.07 for 2–8 photos, then `(ceil(slides/8)+1) × USD 0.07`, at most USD 0.42. Pass the inspected slide count to reject a source changed before confirmation. The source and observation are then frozen. The watch's monthly budget, account cap and worker-wide cap are checked atomically before spending. Unknown costs retain their reservation. Intelligence operations consume zero generation credits.

Collection and analysis return `{ run, max_reservation_usd, credits_used: 0 }`, a durable job rather than an immediate result. Read the dashboard and post to retrieve the outcome. After a timeout, reuse the same key and inspect the job; never submit a new key to repeat an uncertain paid start. Uncertainty pauses the watch. Pausing cancels unstarted work; a known provider run is still read to recover its outcome and cost.

Dated metric observations are append-only. Follow-ups at +24 hours, +72 hours and +7 days start from the first actual observation in each activated monitoring period and are grouped into bounded batches. Comparisons require five other distinct posts from the same account, platform, format and comparable age. Missing metrics remain `null`; competitor Instagram reach, saves and views are never fabricated. Vision analysis covers every photo in order, and recommendations remain hypotheses. Ordinary examples act as controls. Trends describe the observed sample.

To adapt a structure, read `get_carousel_pattern`, write original copy from the client's own topic and Brand Kit, then call `create_carousel` or `update_carousel`. Include `content.inspiration: { analysis_id, source_hash }` to retain a validated reference to that analysis version. Source images and copy are never imported. Adaptation does not call Apify.

```ts
const prepared = await client.createCarouselWatch(clientId, {
  platform: "instagram", kind: "account", target: "creator",
  monthlyBudgetUsd: 3, intervalHours: 24, autoAnalyze: false, analysisLanguage: "en",
}, crypto.randomUUID());
// After the user approves the provider budget:
await client.collectCarouselWatch(clientId, prepared.id, crypto.randomUUID(), true);
const dashboard = await client.getCarouselIntelligence(clientId, "instagram");
// Optional filter on saved watch niche tags:
const nichePatterns = await client.getCarouselPatterns(clientId, "instagram", "travel");
// After inspecting a post and approving its analysis cost:
await client.analyzeSocialCarousel(clientId, postId, "en", crypto.randomUUID(), true, slideCount);
const source = await client.getCarouselPattern(clientId, analysisId);
```

The SDK also provides `setCarouselWatch`, `getSocialCarousel` and `getCarouselPatterns`. The same tool names work through the Claude, OpenAI and Grok MCP configurations above; update `allowed_tools` accordingly. GPT Actions schemas include all eight intelligence actions. Code availability does not confirm deployment or an end-to-end connection in each third-party product.

### Higgsfield and video storyboards

`studioTurnRequestSchema` also accepts `medium?: "photo" | "carousel" | "video"` and `videoPlanId?: UUID | null` to select the storyboard to edit. That storyboard must belong to the authenticated client's conversation. Agent action `prepare_video` saves a validated storyboard in `step.result.video`. Update backend and companion together to accept the action; no new table is added. The agent has no paid-submission action.

Local `POST /__agent/video-quote`, `video-start`, `video-status`, `video-resume`, `video-stop`, `video-media` and `video-export` require `clientId`, `conversationId`, `planId`. `video-start` additionally requires `quoteId`, `confirmedUsd`; `video-media` requires a `scene` index. `video-list` accepts only `clientId`. Browser/MCP identity and conversation ownership are revalidated. Provider status/cancel URLs are restricted to Higgsfield’s origin and the expected request ID. Media is not publicly served.

A private quote binds client, conversation, storyboard hash, model, inputs, amount and expiry. Submission intent persists before contacting the provider. Recovery uses the same `Idempotency-Key`, never an automatic paid replacement. Each status request submits at most one additional shot. A changed price blocks remaining shots. Media downloads pin DNS, bound time/bytes and reject redirects. Files remain in private `data/video/<clientId>/` storage.

Configure `HF_CREDENTIALS=id:secret` or `HF_API_KEY_ID` + `HF_API_KEY_SECRET` on the companion only. HTTP uses `Authorization: Key id:secret`, `POST /estimate/<model>` → `{usd}`, then POST to the model endpoint; the selected catalog model is `kling-video/v4.0-flash/text-to-video`. No model or subscription fallback occurs. Verify actual access with the target API account before release. FFmpeg runs without a shell or URL inputs, using the client’s private files.


### Studio native photos / Photos natives du Studio

`generate_photo` uses the local personal Codex companion and subscription, not the Images API. It accepts `title`, `brief`, `ratio`, and nullable `projectId`, `slideId`, `expectedRevision` (all three required together to attach a photo). `list_photos` returns client-scoped metadata; `set_image` is a validated carousel edit command. The host owns client identity and deterministic generation IDs. Native `imageGeneration` completion events supply the PNG; model-authored paths and text are not image receipts. `image_generation`, `code_mode` and `code_mode_host` are enabled only for this isolated image turn; MCP, shell, browser, plugins and file-view tools remain disabled. The exact native image model is not advertised.

MCP and authenticated `POST /studio-agent` share three storage actions: `list_studio_images {client_id}`, `get_studio_image {client_id,image_id}`, `save_studio_image {client_id,image,base64}`. The last operation uploads an already generated PNG; it does not start inference. Metadata follows `shared/studio-image.ts`; maximum PNG size is 20 MiB. Storage is private, signed URLs last 15 minutes, and reads/writes check both user and client. The originating conversation must belong to that client. Different metadata or bytes for an existing image ID return `idempotency_key_conflict`.

Migration `20261110184227_studio_images` creates owner-readable metadata and service-only writes, plus a private `studio-images` bucket. Carousel asset sources can reference `{kind:"studio",imageId}` only after server ownership verification. Completed native bytes are checkpointed privately under the account/client before cloud upload. Interrupted generation without a checkpoint returns `IMAGE_GENERATION_UNCERTAIN`; retries never silently regenerate. Browser image views and carousel previews revalidate scope. Reference-image uploads and photo editing are outside this initial native-generation path.

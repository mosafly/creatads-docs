# MCP integration and Codex plugin

Endpoint: `https://api.creatads.co/mcp`. V3 ships with the `agentic_creative_workflow` migration and matching imgproc deployment. Always verify `get_generation_capabilities` and `tools/list` against the actual endpoint; local files alone do not prove deployment.

## Codex installation

The [public integrations repository](https://github.com/mosafly/creatads-integrations) contains the plugin and seven skills, without backend source or customer data. Add its directory as a Codex marketplace, then install `creatads@creatads-public`.

The HTTP configuration references the environment variable **name** `CREATADS_MCP_TOKEN`. Set its value in your local secret environment, never in a manifest or conversation. Restart Codex if its process environment changed and start a new task after installation.

The stdio server in `services/mcp` forwards tool discovery and calls to the same HTTP endpoint. It reads this variable or the CreatAds CLI configuration. Both transports therefore use one catalog and contract.

## Workflows

| Skill | Purpose |
|---|---|
| creatads-guide | Discover and choose relevant capabilities |
| creatads-brand | Brand kit, product facts and references |
| creatads-inspiration | Browse, search, favorites and templates |
| creatads-create | Easy/Pro creative generation |
| creatads-edit | Targeted edits and reformatting |
| creatads-review | Fidelity separately from marketing analysis |
| creatads-library | Retrieve and display existing images |

The agent inspects existing context before asking questions. Presets, angles, offers and CTAs are optional choices, not mandatory ingredients of every editorial visual.

## Generation contract

All 27 presets and modes share their catalog with the app. Mode means operation, preset means scene, angle means message and aspect ratio means canvas shape.

Easy Mode supports 1–3 images and the ratios returned in `easy_mode_aspect_ratios`. Pro supports all five ratios and two mutually exclusive shapes:

- `items`: exactly one image per item, for independently art-directed concepts;
- a deliberate matrix of `modes × aspect_ratios × angle_ids × repetitions`. Presets replace scenes and neutralize repetitions.

Maximum 60 images per batch. Invalid combinations are rejected rather than silently normalized.

`preview_easy_generation` and `preview_pro_batch` save the immutable plan: preview/session ids, exact image count, credit estimate, initial/correction budgets and per-image effective prompt, references, product version, mode, preset, ratio, angle and copy.

A preview creates no image and charges no generation credit. Show it to the user, then call the matching `generate_*` with `confirm: true` and a stable `idempotency_key` after approval. Reuse existing ids after a timeout.

## Product facts and fidelity

`list_product_briefs`, `get_product_brief` and `upsert_product_brief` manage versioned client-scoped product facts: description, sourced approved claims, forbidden claims, preservation constraints and reference roles (product, packaging, installation, detail, logo, style, source).

Set `validated: true` only after user validation. Updates require `expected_version`; conflicts must not overwrite newer data. A preview snapshots the approved version and effective prompt, so later brand/product changes cannot alter it.

`edit` requires a source and an instruction in either `variation_prompt` or `prompt_override`, never both. Preservation modes reject automatic copy, presets, angles and enabled brand kit inputs that the engine would ignore. Write exact text replacements in `variation_prompt`; use a creation mode for a new ad.

Never invent reviews, ratings, offers or before/after outcomes. `multi-mini` is explicitly unavailable until multiple sourced reviews have an input contract.

## Budgets and durable execution

Each technically successful image costs one generation credit, **including visually rejected results**. Correction budget defaults to 0 and requires approval. Corrections use the original `session_id`, `correction: true` and a `parent_creative_id` from that session. Original versions remain available.

Activation atomically reserves MCP batch credits. `get_quota_status` distinguishes used, reserved and available credits. Provider request ids and renewable worker leases support recovery. Uncertain provider acceptance is quarantined with its reservation intact, never blindly resubmitted.

This guarantee covers MCP batches. New application requests account for reservations, but an already-running legacy/UI generation is not a durable MCP slot: this is not a global lock across every concurrent entry point.

`get_batch_status` returns per-image results and may recover already-authorized work. `resume_batch` retries known failures within the approved budget. `cancel_batch` cancels pending work; submitted images may still finish and be charged.

## Brand, Inspiration and images

`extract_brand` returns suggestions without overwriting the kit; `update_brand_kit` patches approved fields only. Browse existing references with `list_inspiration`, search through the existing gated service with `search_inspiration`, inspect with `get_inspiration_ad`, and save only selected favorites/templates. Search quotas are separate from generation credits. No competitor watch is started.

`get_creative(include_image: true)` returns native MCP image content, a resource link and structured data, with legacy JSON text compatibility. Rendering depends on the host: prefer native inline media, then an authorized preview or usable link.

`validate_creative` checks product fidelity, dimensions and copy with `passed`, `failed` or `uncertain`. Missing evidence is never a pass. `analyze_creative` separately evaluates marketing potential; its score proves neither fidelity, actual sales nor Meta approval.

## Compatibility and exclusions

Legacy campaign tools remain for existing integrations, but new workflows must use preview/confirmation. Retrieval or review does not authorize paid regeneration. Meta publishing, ad spend, video, OAuth, Figma and automatic monitoring are outside this release.

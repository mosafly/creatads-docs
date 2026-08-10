# TypeScript SDK

`@creatads/sdk` version 1.0.0 is currently a private monorepo package in `services/sdk`; it is not documented as a public npm release.

```typescript
import { CreatadsClient } from "@creatads/sdk";

const creatads = new CreatadsClient(process.env.CREATADS_API_KEY!);
```

The optional second constructor argument overrides the default REST endpoint.

## Methods

```typescript
await creatads.listClients();
await creatads.createClient("Maison Leon");

await creatads.listCampaigns(clientId);
await creatads.createCampaign({
  client_id: clientId,
  name: "Summer Sale",
  cta_text: "Shop now",
  offer_text: "20% off",
  aspect_ratio: "1:1,9:16",
  volume: 4,
  selected_angle_ids: [angleId],
  platform_target: ["facebook", "instagram"],
  landing_url: "https://example.com",
  reference_image_url: "https://example.com/reference.jpg",
  product_image_url: "https://example.com/product.png",
});

await creatads.listCreatives(campaignId);
await creatads.generateCreatives(campaignId);
await creatads.prepareCampaign(campaignId);

await creatads.listAngles(clientId);
await creatads.getAngle(angleId);
await creatads.generateAngles(clientId, researchSummary, "en");

await creatads.getBrandKit(clientId);
```

API-key management requires a Supabase session JWT:

```typescript
await creatads.listApiKeys(jwt);
await creatads.createApiKey(jwt, "Production");
await creatads.revokeApiKey(jwt, keyId);
```

## Generation and polling

```typescript
import { CreatadsClient, pollUntilDone } from "@creatads/sdk";

const creatads = new CreatadsClient(process.env.CREATADS_API_KEY!);
const job = await creatads.generateCreatives(campaignId);

const available = await pollUntilDone(creatads, campaignId, {
  timeoutMs: 180_000,
  intervalMs: 3_000,
  onProgress: (count) => console.log(`${count} image(s) available`),
});
```

Current `pollUntilDone` behavior is deliberately simple: it returns as soon as `listCreatives` contains at least one item. It does not wait for a stable or expected final count. For multi-image batches, continue polling until your expected count is reached.

## Errors

Failed requests throw `CreatadsError` with `code`, `message` and `statusCode`. Common codes are `invalid_api_key`, `premium_required`, `forbidden`, `quota_exceeded`, `missing_param` and `not_found`.

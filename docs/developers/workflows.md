# Developer workflows and limitations

## Onboard a workspace

```bash
creatads clients create "Maison Leon"
creatads clients use <client-id>
creatads angles generate \
  --summary "French artisan chocolate brand, premium positioning, gift market, bean-to-bar production" \
  --language en
creatads angles list
```

Angle generation creates ten reusable creative directions and consumes no image credit.

## Generate a campaign with the CLI

```bash
creatads campaigns create \
  --name "Weekend Offer" \
  --cta "Shop now" \
  --offer "Free delivery" \
  --aspect-ratio "1:1,9:16" \
  --volume 4 \
  --landing-url "https://example.com/weekend"

creatads campaigns generate <campaign-id> --no-wait
creatads creatives list <campaign-id>
```

One successfully generated image consumes one credit. The API rejects the whole batch when it cannot fit inside the remaining quota.

## Generate with the SDK

```typescript
const campaign = await creatads.createCampaign({
  client_id: clientId,
  name: "Weekend Offer",
  cta_text: "Shop now",
  offer_text: "Free delivery",
  aspect_ratio: "1:1,9:16",
  volume: 4,
  selected_angle_ids: [angleId],
});

await creatads.generateCreatives(campaign.id);

let creatives = [];
while (creatives.length < 4) {
  await new Promise((resolve) => setTimeout(resolve, 15_000));
  creatives = await creatads.listCreatives(campaign.id);
}
```

Use an explicit expected-count loop when the complete batch matters. The SDK helper returns after the first available creative.

## API volume behavior

The REST API computes `combinations x max(1, floor(volume / combinations))`, where combinations are selected angles (or one empty angle) times ratios. For exact batch sizing, choose a volume at least as large as and divisible by `angles x ratios`; otherwise the returned `jobs` count can differ from the requested volume.

This contract differs from the web application's Mode Pro matrix, which combines formats, modes, angles and effective volume. Presets in the application neutralize volume to one, and batches above 60 require confirmation rather than being rejected by a hard API limit.

## Application-only workflows

The following cannot currently be automated through the public REST API, SDK, CLI or MCP:

- Inspiration rail and Explorer pool;
- Ads Library manual search and competitor onboarding;
- Clone and ad-structure analysis;
- the five ad-format presets and other Mode Pro modes;
- favorites and winners, which are still placeholders.

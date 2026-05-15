# Workflows

Real-world examples for agencies, developers, and AI agents.

---

## 1. Onboard a new client in 2 minutes

```bash
# Create workspace
creatads clients create "Maison Léon"
creatads clients use <new-id>

# Generate profiles from brand description
creatads angles generate \
  --summary "French artisan chocolate brand, premium positioning, gift market, targeting 30-55 year olds looking for quality gifts for special occasions. Key differentiator: bean-to-bar, single origin, Parisian workshops." \
  --language fr

# Review profiles and note the IDs of the 2-3 most relevant
creatads angles list
```

Total: ~2 min. Client is ready to generate campaigns.

---

## 2. Launch a promo campaign end-to-end

```bash
# 1. Create campaign
creatads campaigns create \
  --name "Saint-Valentin 2027" \
  --cta "Offrir maintenant" \
  --offer "Livraison offerte dès 35€" \
  --aspect-ratio "1:1,4:5,9:16" \
  --volume 9 \
  --landing-url "https://maisonleon.fr/saint-valentin"

# 2. Generate (3 formats × 3 creatives = 9 total, ~4 min)
creatads campaigns generate <campaign-id>

# 3. Download URLs for Meta Ads upload
creatads creatives list <campaign-id>
```

---

## 3. Agency workflow — multiple clients

```bash
#!/bin/bash
# generate-weekly-creatives.sh
# Run every Monday for each client

CLIENTS=("client-id-1" "client-id-2" "client-id-3")
OFFER="Nouveauté de la semaine"

for CLIENT_ID in "${CLIENTS[@]}"; do
  echo "Processing client $CLIENT_ID..."

  CAMPAIGN=$(creatads campaigns create \
    --client "$CLIENT_ID" \
    --name "Weekly $(date +%V)" \
    --cta "Voir" \
    --offer "$OFFER" \
    --volume 3 \
    --aspect-ratio "1:1,9:16" 2>&1)

  CAMPAIGN_ID=$(echo "$CAMPAIGN" | grep -oP '[0-9a-f-]{36}' | head -1)
  creatads campaigns generate "$CAMPAIGN_ID" --timeout 300
done
```

---

## 4. TypeScript integration — Next.js API route

```typescript
// app/api/generate-ads/route.ts
import { CreatadsClient, pollUntilDone } from "@creatads/sdk";
import { NextRequest, NextResponse } from "next/server";

const creatads = new CreatadsClient(process.env.CREATADS_API_KEY!);

export async function POST(req: NextRequest) {
  const { clientId, offer, cta, profileIds } = await req.json();

  // Create campaign
  const campaign = await creatads.createCampaign({
    client_id: clientId,
    name: `Auto — ${new Date().toISOString().slice(0, 10)}`,
    cta_text: cta,
    offer_text: offer,
    aspect_ratio: "1:1,9:16",
    volume: 4,
    selected_angle_ids: profileIds,
  });

  // Trigger generation
  await creatads.generateCreatives(campaign.id);

  // Poll until done
  const creatives = await pollUntilDone(creatads, campaign.id, {
    timeoutMs: 240_000,
    onProgress: (n) => console.log(`${n} creatives ready`),
  });

  return NextResponse.json({
    campaign_id: campaign.id,
    creatives: creatives.map((c) => ({ id: c.id, url: c.image_url })),
  });
}
```

---

## 5. Claude Code — full campaign from a brief

In a Claude Code session with the MCP server configured:

```
Prompt: "Je lance une opération flash ce weekend pour ma boutique Maison Léon.
Promo: -25% sur toute la gamme Saint-Valentin.
Génère-moi une campagne Meta avec 4 visuels (feed 1:1 et stories 9:16), 
ciblant les profils 'cadeau premium' et 'occasion spéciale' si disponibles.
CTA: 'En profiter'. Donne-moi les URLs des images générées."
```

Claude will:
1. Call `list_clients` → find Maison Léon
2. Call `list_angles` → find relevant profiles
3. Call `create_campaign` with your brief
4. Call `generate_creatives` (wait=true, ~3-4 min)
5. Return the image URLs

---

## 6. Generate profiles then creatives in one SDK call

```typescript
import { CreatadsClient, pollUntilDone } from "@creatads/sdk";

async function launchFirstCampaign(apiKey: string, brandDescription: string) {
  const creatads = new CreatadsClient(apiKey);

  // 1. Create client
  const client = await creatads.createClient("My Brand");

  // 2. Generate profiles
  const profiles = await creatads.generateAngles(
    client.id,
    brandDescription,
    "fr"
  );

  // 3. Pick the first 2 profiles
  const selectedIds = profiles.slice(0, 2).map((p) => p.id);

  // 4. Create campaign
  const campaign = await creatads.createCampaign({
    client_id: client.id,
    name: "Launch campaign",
    cta_text: "Découvrir",
    aspect_ratio: "1:1",
    volume: 4,
    selected_angle_ids: selectedIds,
  });

  // 5. Generate + wait
  await creatads.generateCreatives(campaign.id);
  const creatives = await pollUntilDone(creatads, campaign.id);

  return creatives.map((c) => c.image_url);
}
```

---

## Tips

**Maximize profile relevance**
The richer your `research_summary`, the better the profiles. Include: target demographics, key pain points, product positioning, market context, tone of voice.

**Aspect ratios**
- `1:1` — Facebook/Instagram Feed
- `4:5` — Instagram Feed (portrait, higher engagement)
- `9:16` — Stories & Reels
- `1.91:1` — Facebook Landscape / Link ads

**Volume strategy**
- 1 profile + 1 format → 1 creative per slot
- Volume is distributed across (profiles × formats)
- For A/B testing: 2 profiles × 2 formats × 2 = 8 creatives total

**Generation time**
- Expect 2–5 min per batch (fal.ai queue)
- Use `--no-wait` / `wait=false` for fire-and-forget in scripts
- Poll with `creatads creatives list <id>` or `pollUntilDone()`

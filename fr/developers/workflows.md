# Workflows

Exemples concrets pour les agences, les développeurs et les agents IA.

---

## 1. Intégrer un nouveau client en 2 minutes

```bash
# Créer l'espace
creatads clients create "Maison Léon"
creatads clients use <new-id>

# Générer des profils depuis une description de marque
creatads angles generate \
  --summary "French artisan chocolate brand, premium positioning, gift market, targeting 30-55 year olds looking for quality gifts for special occasions. Key differentiator: bean-to-bar, single origin, Parisian workshops." \
  --language fr

# Consulter les profils et noter les IDs des 2-3 plus pertinents
creatads angles list
```

Total : ~2 min. Le client est prêt à générer des campagnes.

---

## 2. Lancer une campagne promo de bout en bout

```bash
# 1. Créer la campagne
creatads campaigns create \
  --name "Saint-Valentin 2027" \
  --cta "Offrir maintenant" \
  --offer "Livraison offerte dès 35€" \
  --aspect-ratio "1:1,4:5,9:16" \
  --volume 9 \
  --landing-url "https://maisonleon.fr/saint-valentin"

# 2. Générer (3 formats × 3 créatifs = 9 au total, ~4 min)
creatads campaigns generate <campaign-id>

# 3. Récupérer les URLs pour upload Meta Ads
creatads creatives list <campaign-id>
```

---

## 3. Workflow agence — plusieurs clients

```bash
#!/bin/bash
# generate-weekly-creatives.sh
# À exécuter chaque lundi pour chaque client

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

## 4. Intégration TypeScript — route API Next.js

```typescript
// app/api/generate-ads/route.ts
import { CreatadsClient, pollUntilDone } from "@creatads/sdk";
import { NextRequest, NextResponse } from "next/server";

const creatads = new CreatadsClient(process.env.CREATADS_API_KEY!);

export async function POST(req: NextRequest) {
  const { clientId, offer, cta, profileIds } = await req.json();

  // Créer la campagne
  const campaign = await creatads.createCampaign({
    client_id: clientId,
    name: `Auto — ${new Date().toISOString().slice(0, 10)}`,
    cta_text: cta,
    offer_text: offer,
    aspect_ratio: "1:1,9:16",
    volume: 4,
    selected_angle_ids: profileIds,
  });

  // Déclencher la génération
  await creatads.generateCreatives(campaign.id);

  // Attendre la fin
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

## 5. Claude Code — campagne complète depuis un brief

Dans une session Claude Code avec le serveur MCP configuré :

```
Prompt : "Je lance une opération flash ce weekend pour ma boutique Maison Léon.
Promo: -25% sur toute la gamme Saint-Valentin.
Génère-moi une campagne Meta avec 4 visuels (feed 1:1 et stories 9:16), 
ciblant les profils 'cadeau premium' et 'occasion spéciale' si disponibles.
CTA: 'En profiter'. Donne-moi les URLs des images générées."
```

Claude va :
1. Appeler `list_clients` → trouver Maison Léon
2. Appeler `list_angles` → trouver les profils pertinents
3. Appeler `create_campaign` avec votre brief
4. Appeler `generate_creatives` (wait=true, ~3-4 min)
5. Retourner les URLs des images

---

## 6. Générer des profils puis des créatifs en un seul appel SDK

```typescript
import { CreatadsClient, pollUntilDone } from "@creatads/sdk";

async function launchFirstCampaign(apiKey: string, brandDescription: string) {
  const creatads = new CreatadsClient(apiKey);

  // 1. Créer le client
  const client = await creatads.createClient("My Brand");

  // 2. Générer les profils
  const profiles = await creatads.generateAngles(
    client.id,
    brandDescription,
    "fr"
  );

  // 3. Sélectionner les 2 premiers profils
  const selectedIds = profiles.slice(0, 2).map((p) => p.id);

  // 4. Créer la campagne
  const campaign = await creatads.createCampaign({
    client_id: client.id,
    name: "Launch campaign",
    cta_text: "Découvrir",
    aspect_ratio: "1:1",
    volume: 4,
    selected_angle_ids: selectedIds,
  });

  // 5. Générer + attendre
  await creatads.generateCreatives(campaign.id);
  const creatives = await pollUntilDone(creatads, campaign.id);

  return creatives.map((c) => c.image_url);
}
```

---

## Conseils

**Maximiser la pertinence des profils**
Plus votre `research_summary` est riche, meilleurs seront les profils. Incluez : démographie cible, points de douleur clés, positionnement produit, contexte marché, ton de voix.

**Formats d'image**
- `1:1` — Facebook/Instagram Feed
- `4:5` — Instagram Feed (portrait, engagement plus élevé)
- `9:16` — Stories & Reels
- `1.91:1` — Facebook Landscape / Link ads

**Stratégie de volume**
- 1 angle + 1 format → 1 créatif par slot
- Le volume est distribué entre (profils × formats)
- Pour l'A/B testing : 2 profils × 2 formats × 2 = 8 créatifs au total

**Temps de génération**
- Prévoyez 2–5 min par batch (file fal.ai)
- Utilisez `--no-wait` / `wait=false` pour le fire-and-forget dans les scripts
- Interrogez avec `creatads creatives list <id>` ou `pollUntilDone()`

# TypeScript SDK

## Installation

```bash
npm install @creatads/sdk
```

> Note : actuellement un package local (`services/sdk/`). Sera publié sur npm.

## Configuration

```typescript
import { CreatadsClient } from "@creatads/sdk";

const client = new CreatadsClient(
  process.env.CREATADS_API_KEY!,
  // optionnel : URL de base personnalisée (par défaut : production)
);
```

---

## Clients

```typescript
// Lister tous les clients
const clients = await client.listClients();
// [{ id, name, user_id, created_at, onboarding_completed }]

// Créer un client
const newClient = await client.createClient("Nike France");
// { id, name, user_id, created_at }
```

---

## Profils

```typescript
// Lister les profils d'un client
const profiles = await client.listProfiles(clientId);
// [{ id, client_id, persona, pain_point, angle, created_at }]

// Obtenir un profil unique
const profile = await client.getProfile(profileId);

// Générer 10 profils IA depuis une description de marque
const generated = await client.generateProfiles(
  clientId,
  "French eco-friendly skincare brand targeting urban women 25-40",
  "fr" // langue : "fr" | "en"
);
// Retourne Profile[] — déjà sauvegardés dans le compte
```

---

## Campagnes

```typescript
// Lister les campagnes
const campaigns = await client.listCampaigns(clientId);

// Créer une campagne
const campaign = await client.createCampaign({
  client_id: clientId,
  name: "Summer Sale",
  cta_text: "Découvrir",
  offer_text: "-20% ce weekend",
  aspect_ratio: "1:1,9:16",       // séparé par virgules
  volume: 6,
  selected_profile_ids: [profileId1, profileId2],
  landing_url: "https://example.com",
});

// Déclencher la génération (fire-and-forget)
const job = await client.generateCreatives(campaign.id);
// { job_id, campaign_id, status: "pending" }
```

---

## Créatifs

```typescript
// Lister les créatifs d'une campagne
const creatives = await client.listCreatives(campaignId);
// [{ id, campaign_id, image_url, aspect_ratio, status, created_at }]
```

---

## Attendre la fin de la génération

```typescript
import { pollUntilDone } from "@creatads/sdk";

// Lancer + attendre les résultats
const job = await client.generateCreatives(campaign.id);

const creatives = await pollUntilDone(client, campaign.id, {
  timeoutMs: 180_000,          // timeout 3 min (défaut)
  intervalMs: 3_000,           // vérifier toutes les 3s (défaut)
  onProgress: (count) => {
    console.log(`${count} créatifs prêts...`);
  },
});

console.log(creatives.map((c) => c.image_url));
```

**Comportement :**
- Interroge `listCreatives` toutes les `intervalMs`
- Se résout quand au moins 1 créatif est prêt et que le nombre se stabilise
- Rejette avec `PollTimeoutError` si `timeoutMs` est dépassé

---

## Brand Kit

```typescript
const kit = await client.getBrandKit(clientId);
// { brand_name, brand_description, color_primary, color_secondary, color_accent }
// Retourne null si aucun brand kit configuré
```

---

## Gestion des erreurs

```typescript
import { CreatadsError } from "@creatads/sdk";

try {
  await client.generateCreatives(campaignId);
} catch (e) {
  if (e instanceof CreatadsError) {
    console.error(e.code);        // "premium_required" | "invalid_api_key" | "not_found" | ...
    console.error(e.message);     // Message lisible par un humain
    console.error(e.statusCode);  // Code de statut HTTP
  }
}
```

Codes d'erreur courants :

| Code | Signification |
|---|---|
| `invalid_api_key` | Clé invalide ou révoquée |
| `premium_required` | Abonnement actif requis |
| `forbidden` | La ressource appartient à un autre utilisateur |
| `not_found` | La ressource n'existe pas |
| `missing_param` | Paramètre requis manquant |

---

## Types

```typescript
interface Client {
  id: string;
  name: string;
  user_id: string;
  created_at: string;
  onboarding_completed?: boolean;
}

interface Profile {
  id: string;
  client_id: string;
  persona: string;
  pain_point: string;
  angle: string;
  created_at: string;
}

interface Campaign {
  id: string;
  client_id: string;
  name: string;
  status: string;
  cta_text: string;
  offer_text: string;
  aspect_ratio: string;
  volume: number;
  selected_profile_ids: string[];
  reference_image_url: string | null;
  product_image_url: string | null;
  created_at: string;
}

interface Creative {
  id: string;
  campaign_id: string;
  image_url: string;
  aspect_ratio: string | null;
  status: string;
  created_at: string;
}
```

## Helpers de configuration

```typescript
import { loadConfig, saveConfig, DEFAULT_BASE_URL } from "@creatads/sdk";

// Lire ~/.creatads/config.json
const config = loadConfig();
// { api_key, base_url, default_client_id? }

// Écrire la config (chmod 600)
saveConfig({
  api_key: "cads_...",
  base_url: DEFAULT_BASE_URL,
  default_client_id: "89b18b5f-...",
});
```

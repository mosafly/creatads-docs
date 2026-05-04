# TypeScript SDK

## Installation

```bash
npm install @creatads/sdk
```

> Note: currently a local package (`services/sdk/`). Will be published to npm.

## Setup

```typescript
import { CreatadsClient } from "@creatads/sdk";

const client = new CreatadsClient(
  process.env.CREATADS_API_KEY!,
  // optional: custom base URL (defaults to production)
);
```

---

## Clients

```typescript
// List all clients
const clients = await client.listClients();
// [{ id, name, user_id, created_at, onboarding_completed }]

// Create a client
const newClient = await client.createClient("Nike France");
// { id, name, user_id, created_at }
```

---

## Profiles

```typescript
// List profiles for a client
const profiles = await client.listProfiles(clientId);
// [{ id, client_id, persona, pain_point, angle, created_at }]

// Get a single profile
const profile = await client.getProfile(profileId);

// Generate 10 AI profiles from a brand description
const generated = await client.generateProfiles(
  clientId,
  "French eco-friendly skincare brand targeting urban women 25-40",
  "fr" // language: "fr" | "en"
);
// Returns Profile[] — already saved to the account
```

---

## Campaigns

```typescript
// List campaigns
const campaigns = await client.listCampaigns(clientId);

// Create a campaign
const campaign = await client.createCampaign({
  client_id: clientId,
  name: "Summer Sale",
  cta_text: "Découvrir",
  offer_text: "-20% ce weekend",
  aspect_ratio: "1:1,9:16",       // comma-separated
  volume: 6,
  selected_profile_ids: [profileId1, profileId2],
  landing_url: "https://example.com",
});

// Trigger generation (fire-and-forget)
const job = await client.generateCreatives(campaign.id);
// { job_id, campaign_id, status: "pending" }
```

---

## Creatives

```typescript
// List creatives for a campaign
const creatives = await client.listCreatives(campaignId);
// [{ id, campaign_id, image_url, aspect_ratio, status, created_at }]
```

---

## Poll until done

```typescript
import { pollUntilDone } from "@creatads/sdk";

// Launch + wait for results
const job = await client.generateCreatives(campaign.id);

const creatives = await pollUntilDone(client, campaign.id, {
  timeoutMs: 180_000,          // 3 min timeout (default)
  intervalMs: 3_000,           // poll every 3s (default)
  onProgress: (count) => {
    console.log(`${count} creatives ready...`);
  },
});

console.log(creatives.map((c) => c.image_url));
```

**Behaviour:**
- Polls `listCreatives` every `intervalMs`
- Resolves when at least 1 creative is ready and the count stabilizes
- Rejects with `PollTimeoutError` if `timeoutMs` is exceeded

---

## Brand Kit

```typescript
const kit = await client.getBrandKit(clientId);
// { brand_name, brand_description, color_primary, color_secondary, color_accent }
// Returns null if no brand kit configured
```

---

## Error handling

```typescript
import { CreatadsError } from "@creatads/sdk";

try {
  await client.generateCreatives(campaignId);
} catch (e) {
  if (e instanceof CreatadsError) {
    console.error(e.code);        // "premium_required" | "invalid_api_key" | "not_found" | ...
    console.error(e.message);     // Human-readable message
    console.error(e.statusCode);  // HTTP status code
  }
}
```

Common error codes:

| Code | Meaning |
|---|---|
| `invalid_api_key` | Key invalid or revoked |
| `premium_required` | Active subscription required |
| `forbidden` | Resource belongs to another user |
| `not_found` | Resource doesn't exist |
| `missing_param` | Required parameter missing |

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

## Config helpers

```typescript
import { loadConfig, saveConfig, DEFAULT_BASE_URL } from "@creatads/sdk";

// Read ~/.creatads/config.json
const config = loadConfig();
// { api_key, base_url, default_client_id? }

// Write config (chmod 600)
saveConfig({
  api_key: "cads_...",
  base_url: DEFAULT_BASE_URL,
  default_client_id: "89b18b5f-...",
});
```

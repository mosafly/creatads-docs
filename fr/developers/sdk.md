# SDK TypeScript

`@creatads/sdk` 1.0.0 est actuellement un package privé du monorepo dans `services/sdk`, pas une publication npm publique documentée.

```typescript
import { CreatadsClient } from "@creatads/sdk";
const creatads = new CreatadsClient(process.env.CREATADS_API_KEY!);
```

## Méthodes

```typescript
await creatads.listClients();
await creatads.createClient("Maison Leon");
await creatads.listCampaigns(clientId);
await creatads.createCampaign({
  client_id: clientId,
  name: "Promotion été",
  aspect_ratio: "1:1,9:16",
  volume: 4,
  selected_angle_ids: [angleId],
  reference_image_url: "https://example.com/reference.jpg",
  product_image_url: "https://example.com/produit.png",
});
await creatads.generateCreatives(campaignId);
await creatads.listCreatives(campaignId);
await creatads.prepareCampaign(campaignId);
await creatads.listAngles(clientId);
await creatads.getAngle(angleId);
await creatads.generateAngles(clientId, researchSummary, "fr");
await creatads.getBrandKit(clientId);
```

La gestion des clés exige un JWT Supabase : `listApiKeys(jwt)`, `createApiKey(jwt, name)` et `revokeApiKey(jwt, keyId)`.

## Suivi de génération

```typescript
import { pollUntilDone } from "@creatads/sdk";

await creatads.generateCreatives(campaignId);
const disponibles = await pollUntilDone(creatads, campaignId, {
  timeoutMs: 180_000,
  intervalMs: 3_000,
});
```

`pollUntilDone` retourne dès que `listCreatives` contient au moins un élément. Il n'attend pas la stabilisation ou le nombre final prévu. Pour un lot, continuez à interroger jusqu'au nombre attendu.

Les erreurs lèvent `CreatadsError` avec `code`, `message` et `statusCode`.

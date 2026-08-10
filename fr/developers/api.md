# Référence API REST

Toutes les opérations utilisent un seul endpoint fondé sur des actions :

```text
POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
```

Envoyez `Content-Type: application/json`, une clé valide dans `X-Api-Key` et une propriété `action` dans le corps JSON.

```bash
curl -X POST "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api" \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $CREATADS_API_KEY" \
  -d '{"action":"list_clients"}'
```

## Actions disponibles

| Action | Champs obligatoires | Résultat |
|---|---|---|
| `list_clients` | aucun | `{ clients }` |
| `create_client` | `name` | `{ client }` |
| `list_campaigns` | `client_id` | `{ campaigns }` |
| `create_campaign` | `client_id` | `{ campaign }` |
| `list_creatives` | `campaign_id` | `{ creatives }` |
| `list_angles` | `client_id` | `{ angles }` |
| `get_angle` | `angle_id` | `{ angle }` |
| `generate_angles` | `client_id`, `research_summary` | `{ angles }` |
| `get_brand_kit` | `client_id` | `{ brand_kit }` |
| `prepare_campaign` | `campaign_id` | Copy et recommandations préparées |
| `generate_creatives` | `campaign_id` | Métadonnées du job en attente |

`list_api_keys`, `create_api_key` et `revoke_api_key` utilisent uniquement un JWT Supabase.

## Créer une campagne

```json
{
  "action": "create_campaign",
  "client_id": "uuid",
  "name": "Promotion été",
  "cta_text": "Découvrir",
  "offer_text": "-20 % ce week-end",
  "aspect_ratio": "1:1,9:16",
  "volume": 4,
  "selected_angle_ids": ["angle-uuid"],
  "platform_target": ["facebook", "instagram"],
  "landing_url": "https://example.com/promo",
  "reference_image_url": "https://example.com/reference.jpg",
  "product_image_url": "https://example.com/produit.png"
}
```

Seul `client_id` est obligatoire. Les valeurs par défaut sont : `Untitled Campaign`, ratio `1:1`, volume `1`, textes/listes vides et aucune URL d'image. Cette action force actuellement `use_brand_kit` à `true`.

## Générer puis suivre

```json
{ "action": "generate_creatives", "campaign_id": "uuid" }
```

La réponse contient `job_id`, `campaign_id`, `status: "pending"` et le nombre de jobs. La génération est asynchrone : interrogez `list_creatives` jusqu'au nombre attendu.

Chaque image réussie consomme un crédit. Le lot complet est vérifié par rapport au quota restant avant son lancement.

Le nombre effectif de jobs est `combinaisons x max(1, floor(volume / combinaisons))`, où les combinaisons correspondent aux angles sélectionnés (ou un angle vide) multipliés par les ratios. Un volume non divisible est arrondi vers le bas ; un volume inférieur au nombre de combinaisons produit un job par combinaison et peut donc être dépassé. Le champ `jobs` de la réponse fait foi.

L'API n'expose pas la matrice complète modes/presets de Mode Pro.

## Générer des angles

```json
{
  "action": "generate_angles",
  "client_id": "uuid",
  "research_summary": "Marque de soins premium pour femmes urbaines de 25 à 40 ans.",
  "language": "fr"
}
```

Cette action crée dix angles et ne consomme pas de crédit image.

Il n'existe actuellement aucune action publique pour Inspiration, Explorer, la recherche Ads Library, Clone, Favoris ou Winners.

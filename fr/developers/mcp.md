# Intégration MCP

CreatAds fournit deux transports MCP aux contrats légèrement différents.

## MCP HTTP distant

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

Le serveur distant expose onze outils :

- `list_clients`, `create_client` ;
- `upload_image` ;
- `list_campaigns`, `create_campaign` ;
- `generate_creatives`, `list_creatives` ;
- `list_angles`, `get_angle`, `generate_angles` ;
- `get_brand_kit`.

`generate_creatives` retourne immédiatement en HTTP. Interrogez `list_creatives` environ toutes les 15 secondes jusqu'au nombre attendu.

`upload_image` accepte une image JPEG, PNG ou WebP encodée en base64 et renvoie une URL utilisable comme `reference_image_url` ou `product_image_url`.

## MCP stdio local

Le package local `creatads-mcp` vit dans `services/mcp` et lit `~/.creatads/config.json`. Exécutez d'abord `creatads auth login`.

Il expose dix outils : le même ensemble, sans `upload_image`. Son outil de génération accepte `wait` et attend par défaut, mais le poller partagé retourne à la première créative disponible.

## Séquence recommandée

```text
list_clients
-> list_angles ou generate_angles
-> upload_image (distant uniquement, si nécessaire)
-> create_campaign
-> generate_creatives
-> list_creatives jusqu'au nombre attendu
```

MCP n'expose pas Inspiration, Explorer, la recherche Ads Library, Clone, Favoris, Winners ou la matrice complète de Mode Pro.

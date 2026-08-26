# Intégration MCP

Endpoint HTTP distant : `https://api.creatads.co/mcp`

> Le contrat ci-dessous est la V2. Elle devient disponible après application de
> la migration `mcp_generation_batches` et déploiement du service imgproc.
> Consulte toujours `tools/list` : c'est la source de vérité de l'instance déployée.

## Connexion

Authentifie le client avec une clé API CreatAds dans l'en-tête Bearer.

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

Pour Codex, configure le même endpoint HTTP avec une variable d'environnement
contenant la clé, jamais une clé écrite dans le dépôt.

## Workflow V2 recommandé

```text
1. get_generation_capabilities
2. list_clients, get_brand_kit, list_angles et list_assets
3. upload_image ou ingest_external_image si nécessaire
4. preview_easy_generation ou preview_pro_batch
5. relire total_images et estimated_credits
6. generate_* avec confirm: true et une idempotency_key unique
7. get_batch_status jusqu'à la fin ; resume_batch ou cancel_batch si nécessaire
```

Les previews ne consomment pas de crédit. Une image réellement terminée consomme
un crédit ; une confirmation explicite est donc obligatoire avant l'exécution.

## Outils V2

- Discovery : `get_generation_capabilities`, `list_creative_modes`,
  `list_formats`, `list_presets`.
- Contexte : les outils existants `list_clients`, `create_client`,
  `get_brand_kit`, `list_angles`, `get_angle`, `generate_angles`.
- Assets : `upload_image`, `ingest_external_image`, `list_assets`,
  `list_templates`.
- Mode Facile : `preview_easy_generation`, `generate_easy_generation`.
- Mode Pro : `preview_pro_batch`, `generate_pro_batch`, `get_batch_status`,
  `resume_batch`, `cancel_batch`.

Mode Facile accepte 1 à 3 variantes ou un slot par preset. Mode Pro calcule le
produit cartésien réel `ratios × modes/scènes × angles × répétitions`, avec une
limite de 60 images. Un preset neutralise les répétitions, comme dans l'app.

`cancel_batch` annule les slots qui ne sont pas encore lancés. Une image déjà
en cours auprès du fournisseur peut se terminer, mais ne relance pas le batch.

## Compatibilité V1

Les onze outils historiques, dont `create_campaign` et `generate_creatives`,
restent présents temporairement pour les intégrations existantes. Ils reposent
sur l'ancien modèle de campagne et ne doivent pas être utilisés pour de
nouvelles intégrations : ils ne couvrent ni Mode Facile, ni presets, ni le
preview de coût, ni le batch durable V2.

## Hors périmètre actuel

L'exploration Ads Library et la publication Meta ne sont pas exposées dans la
V2. La publication Meta reste désactivée côté produit pendant la phase
Analytics.

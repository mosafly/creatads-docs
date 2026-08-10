# Authentification

CreatAds utilise deux mécanismes distincts.

| Identifiant | En-tête | Opérations |
|---|---|---|
| Clé API CreatAds | `X-Api-Key: cads_...` | Espaces, campagnes, créatives, angles et Brand Kit |
| JWT de session Supabase | `Authorization: Bearer <jwt>` | Créer, lister ou révoquer les clés API |

## Clés API

Une clé commence par `cads_`, suivi de 64 caractères hexadécimaux. Elle appartient au compte utilisateur et n'est affichée qu'une fois.

Créez ou révoquez les clés dans **Paramètres > API**, puis stockez-les dans un gestionnaire de secrets ou une variable d'environnement :

```bash
CREATADS_API_KEY=cads_...
```

```bash
curl -X POST "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api" \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $CREATADS_API_KEY" \
  -d '{"action":"list_clients"}'
```

Ne commitez jamais une clé. Révoquez-la immédiatement si elle est exposée.

## Gestion des clés via CLI ou SDK

Ces opérations exigent le JWT Supabase de la session courante, pas une clé CreatAds existante.

```bash
creatads keys list --jwt <supabase-session-jwt>
creatads keys create --name "Production" --jwt <supabase-session-jwt>
creatads keys revoke <key-id> --jwt <supabase-session-jwt>
```

Sans `--jwt`, le CLI le demande. Le tableau de bord reste le chemin le plus simple.

## Erreurs courantes

| HTTP | Code | Signification |
|---:|---|---|
| 401 | `invalid_api_key` | Clé absente, malformée, inconnue ou révoquée |
| 403 | `premium_required` | Le compte n'a pas accès à l'API |
| 403 | `forbidden` | La ressource n'appartient pas au compte |
| 403 | `quota_exceeded` | Le lot dépasse les crédits restants |
| 400 | `missing_param` | Champ obligatoire absent |
| 404 | `not_found` | Ressource introuvable |

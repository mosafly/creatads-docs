# Authentification

## Clés API

CreatAds utilise des clés API pour tous les accès développeur. Les clés :
- Commencent par `cads_` suivi de 64 caractères hexadécimaux
- Sont liées à votre compte utilisateur (pas à un espace client)
- Nécessitent un abonnement premium actif pour fonctionner
- Peuvent être révoquées à tout moment depuis le dashboard ou le CLI

### Générer une clé

1. Allez dans **Paramètres → API** dans le dashboard CreatAds
2. Cliquez sur **Nouvelle clé API**, donnez-lui un nom (ex. "Production", "Claude Code")
3. Copiez la clé — **elle n'est affichée qu'une seule fois**

Ou via le CLI (nécessite d'être connecté) :
```bash
creatads keys create --name "Mon intégration"
```

### Utiliser une clé

Passez la clé dans l'en-tête `X-Api-Key` :

```bash
curl -X POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: cads_your_key_here" \
  -d '{"action": "list_clients"}'
```

Le SDK et le CLI gèrent cela automatiquement une fois configurés.

## Sécurité

**Ne committez jamais votre clé API.** Utilisez des variables d'environnement :

```bash
# .env
CREATADS_API_KEY=cads_...
```

```typescript
import { CreatadsClient } from "@creatads/sdk";
const client = new CreatadsClient(process.env.CREATADS_API_KEY!);
```

**Faites tourner vos clés régulièrement.** Si une clé est compromise :
```bash
creatads keys list          # trouver l'ID de la clé
creatads keys revoke <id>   # révoquer immédiatement
creatads keys create --name "Remplacement"
```

## Erreurs

| Statut HTTP | Code d'erreur | Signification |
|---|---|---|
| 401 | `invalid_api_key` | Clé introuvable, révoquée ou malformée |
| 403 | `premium_required` | Aucun abonnement actif |
| 403 | `forbidden` | La ressource n'appartient pas à votre compte |
| 400 | `missing_param` | Paramètre requis non fourni |
| 404 | `not_found` | La ressource n'existe pas |
| 500 | — | Erreur serveur (réessayez) |

## Deux modes d'authentification

La fonction edge `/api` supporte deux modes d'authentification :

| En-tête | Actions | Cas d'usage |
|---|---|---|
| `X-Api-Key: cads_...` | Toutes les actions métier (generate, list, create) | Usage API normal |
| `Authorization: Bearer <jwt>` | Gestion des clés uniquement (create/list/revoke) | Dashboard uniquement |

La gestion des clés via `Authorization` nécessite un JWT Supabase valide (token de session de l'application web). C'est intentionnel — vous ne pouvez pas créer de nouvelles clés API en utilisant une clé API existante.

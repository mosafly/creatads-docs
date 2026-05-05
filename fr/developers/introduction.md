# CreatAds — Plateforme développeur

> Générez des créatifs publicitaires IA par programme — depuis votre terminal, votre code ou votre agent IA.

## Qu'est-ce que la plateforme développeur CreatAds ?

CreatAds expose l'intégralité de son pipeline de génération via trois interfaces :

| Interface | Idéal pour |
|---|---|
| **CLI** (`creatads-cli`) | Workflows terminal, scripts, CI/CD |
| **TypeScript SDK** (`@creatads/sdk`) | Applications Node.js, intégrations personnalisées |
| **MCP Server** (`creatads-mcp`) | Claude Code, agents IA, workflows LLM |

Les trois partagent la même authentification (clé API) et la même API REST sous-jacente.

## Prérequis

- Un compte CreatAds sur [creatads.co](https://creatads.co)
- Un **abonnement actif** (Studio, Agence ou Scale) — la plateforme développeur est réservée aux abonnés premium
- Une clé API — générez-en une dans **Paramètres → API**

## Démarrage rapide (5 minutes)

### 1. Installer le CLI

```bash
npm install -g creatads-cli
```

### 2. S'authentifier

```bash
creatads auth login
# Collez votre clé API quand on vous la demande (commence par cads_...)
```

### 3. Définir votre client par défaut

```bash
creatads clients list
creatads clients use <client-id>
```

### 4. Générer votre première campagne

```bash
# Générer des profils d'audience depuis votre description de marque
creatads profiles generate \
  --summary "French e-commerce brand selling eco-friendly home products to urban millennials" \
  --language fr

# Créer une campagne
creatads campaigns create \
  --name "Summer Sale 2026" \
  --cta "Découvrir" \
  --offer "-20% ce weekend" \
  --aspect-ratio "1:1,9:16" \
  --volume 6

# Générer les créatifs (attend ~3 min, affiche un spinner)
creatads campaigns generate <campaign-id>
```

## Architecture

```
Votre code / agent
       │
       ▼
┌─────────────────┐
│  REST API        │  POST https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
│  X-Api-Key auth  │
└────────┬────────┘
         │
    ┌────┴────────────────────┐
    │                         │
    ▼                         ▼
Supabase Edge Functions   imgproc Bun Service
(auth, DB, billing)       api.creatads.co
                          (Génération IA : fal.ai, OpenRouter)
```

## Sections

- [Authentification](./authentication.md) — Clés API, sécurité
- [Référence CLI](./cli.md) — Toutes les commandes avec exemples
- [TypeScript SDK](./sdk.md) — Référence `@creatads/sdk`
- [MCP Server](./mcp.md) — Intégration Claude Code & agents IA
- [Workflows](./workflows.md) — Exemples concrets

## Limites de débit & quotas

Les quotas de génération sont partagés avec l'application web — la même limite mensuelle s'applique quelle que soit la façon dont vous générez.

| Plan | Générations/mois |
|---|---|
| Studio | 60 |
| Agence | 250 |
| Scale | Illimité |

Les requêtes API (list, create, etc.) ne sont pas comptées dans votre quota — seuls les appels de génération le sont.

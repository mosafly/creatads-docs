# Carrousels Agent First : Claude, ChatGPT et Grok

L'agent rédige directement le contenu organique, l'enregistre dans CreatAds, modifie les slides et récupère les fichiers prêts à publier. Ce parcours fonctionne sans navigateur. Instagram/TikTok, contenus éditoriaux/produit, 3–10 slides et ratios `1:1`, `4:5`, `9:16` partagent le même contrat pour l'application, l'API et le MCP.

La disponibilité dépend du déploiement imgproc et de l'Edge Function `api` avec la migration `agent_carousels`. Vérifiez `get_carousel_capabilities` sur votre instance avant de commencer. Les exemples ci-dessous décrivent les intégrations à configurer ; ils ne signifient pas que vos comptes sont déjà connectés.

## Parcours

1. Appeler `list_clients`, puis lire le Brand Kit et `list_assets` du client choisi. Utiliser des faits produit vérifiés.
2. Rédiger les titres, textes, légende et jusqu'à cinq hashtags dans l'agent.
3. Appeler `create_carousel` avec le contenu et une `idempotency_key` stable. Le document reste en brouillon.
4. Si nécessaire, générer les visuels avec `preview_pro_batch` : un `item` par slide, ratio du carrousel, `prompt_override` contenant le prompt visuel exact et le titre/texte de la slide comme contexte à ne pas imprimer, `use_angle: false`, `use_brand_kit: false`. Décrire une scène photographique concrète sans texte, logo ni interface lisible. La politique serveur sélectionne GPT Image 2.5 Flare en qualité haute. Ajouter les références produit appartenant au client. Montrer le plan et son coût, obtenir l'approbation, puis `generate_pro_batch` et `get_batch_status`.
5. Lire `get_carousel`, reporter les `creative_id` réussis dans les slides avec `update_carousel` et `expected_version`. Conserver les IDs des slides lors des modifications ou réordonnancements.
6. Inspecter avec `render_carousel_slide`, puis `export_carousel` pour la version approuvée.

Le texte fourni par l'agent, les modifications, les rendus et l'export coûtent **0 crédit CreatAds**. Les visuels suivent la facturation existante : un crédit par image techniquement réussie. La génération de trame native dans l'app reste une opération distincte à un crédit. Les frais du modèle de l'agent sont facturés séparément par son fournisseur.

## Outils et actions API

| Nom | Paramètres |
|---|---|
| `get_carousel_capabilities` | aucun |
| `create_carousel` | `client_id`, `idempotency_key`, `content` |
| `list_carousels` | `client_id`, `limit` (30 par défaut), `offset` (0 par défaut) |
| `get_carousel` | `client_id`, `carousel_id` |
| `update_carousel` | `client_id`, `carousel_id`, `expected_version`, `content` complet |
| `render_carousel_slide` | `client_id`, `carousel_id`, `expected_version`, `slide_id` |
| `export_carousel` | `client_id`, `carousel_id`, `expected_version` |

`create_carousel` et `update_carousel` renvoient `{ carousel, credits_used: 0 }`. Le document contient `id`, `client_id`, `version`, `status: "draft"`, `content`, `created_at`, `updated_at`. Les IDs de slides omis à la création sont attribués par le serveur. `get_carousel` ajoute `images`, un dictionnaire `slide_id → URL` des images vérifiées. `list_carousels` renvoie `carousels` et `next_offset` (ou `null`).

Les images de slide utilisent **un seul** `asset_id` ou `creative_id`, appartenant au client et hébergé dans CreatAds. Aucune URL arbitraire n'est acceptée dans le contenu. Une création rejouée avec la même clé et le même contenu retrouve le document ; un autre contenu sous cette clé produit `idempotency_key_conflict`. Une modification concurrente produit `version_conflict` (HTTP 409 via API). Relire et réconcilier ; ne pas écraser automatiquement.

L'export renvoie `archive`, `slides` ordonnées, dimensions et `expires_at`. Les fichiers sont privés et accessibles par liens signés pendant une heure. Le ZIP contient les PNG, `caption.txt`, `carousel.json` compatible avec l'import UI et `document.json` avec l'identité/version cloud. Rien n'est publié sur Instagram ou TikTok.

```json
{
  "action": "create_carousel",
  "client_id": "11111111-1111-4111-8111-111111111111",
  "idempotency_key": "editorial-cafe-001",
  "content": {
    "platform": "instagram", "kind": "editorial", "language": "fr",
    "ratio": "4:5", "theme": "light", "accent": "#7c3aed", "brand_name": "Mon café",
    "title": "Trois gestes pour votre café", "caption": "Quel est votre rituel ?", "hashtags": ["#cafe"],
    "slides": [
      { "title": "Un café à votre goût", "body": "Commencez par observer votre préparation.", "visual_prompt": "Minimal coffee scene, no typography" },
      { "title": "Notez vos préférences", "body": "Gardez une trace des saveurs que vous appréciez.", "visual_prompt": "Coffee cup and blank notebook, no text" },
      { "title": "Partagez votre rituel", "body": "Quel geste compte le plus pour vous ?", "visual_prompt": "Hands holding a coffee cup, no text" }
    ]
  }
}
```

Remplacer l'UUID par celui de `list_clients`. Envoyer ce JSON à l'[endpoint API](/fr/developers/api), avec la clé dans `X-Api-Key`. Les vérifications d'accès de l'API restent applicables. Pour les outils de génération, utiliser `{ "action": "call_tool", "name": "preview_pro_batch", "arguments": { ... } }` : validation et exécution sont celles du MCP, sans deuxième contrat.

## MCP distant

Endpoint : `https://api.creatads.co/mcp`, transport HTTP, clé CreatAds dans `Authorization: Bearer …`. Le transport stdio transmet ce catalogue automatiquement. Découvrir les schémas avec `tools/list` ; ne pas inventer d'identifiant. Après un timeout de création, réutiliser la même clé. Après un timeout de génération, interroger le batch connu.

### Claude API

Le [connecteur MCP de la Messages API](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) utilise `mcp_servers`, `authorization_token` et un `mcp_toolset`. La configuration suivante cible notre authentification Bearer ; choisir un modèle disponible sur votre compte et garder les secrets dans l'environnement.

```python
import os
import anthropic
response = anthropic.Anthropic().beta.messages.create(
    model=os.environ["CLAUDE_MODEL"], max_tokens=2000,
    betas=["mcp-client-2025-11-20"],
    mcp_servers=[{"type": "url", "url": "https://api.creatads.co/mcp",
                  "name": "creatads", "authorization_token": os.environ["CREATADS_MCP_TOKEN"]}],
    tools=[{"type": "mcp_toolset", "mcp_server_name": "creatads"}],
    messages=[{"role": "user", "content": "Lis les capacités de carrousel et mes espaces clients."}],
)
```

### OpenAI Responses API

La [Responses API](https://developers.openai.com/api/docs/guides/tools-connectors-mcp) accepte un serveur MCP distant et son token. L'approbation de transmission/appel côté OpenAI et la confirmation du budget d'images CreatAds restent deux contrôles distincts.

```python
import os
from openai import OpenAI
response = OpenAI().responses.create(
    model=os.environ["OPENAI_MODEL"],
    input="Lis les capacités de carrousel et mes espaces clients.",
    tools=[{"type": "mcp", "server_label": "creatads",
            "server_url": "https://api.creatads.co/mcp",
            "authorization": os.environ["CREATADS_MCP_TOKEN"],
            "allowed_tools": ["get_carousel_capabilities", "list_clients"],
            "require_approval": "always"}],
)
```

### Grok / xAI API

Les [outils MCP distants xAI](https://docs.x.ai/developers/tools/remote-mcp) utilisent la même URL et `authorization`. xAI ne prend pas en charge `require_approval` : ne pas recopier ce paramètre OpenAI. Le parcours preview/confirmation CreatAds reste obligatoire pour les images payantes.

```python
import os
from openai import OpenAI
client = OpenAI(api_key=os.environ["XAI_API_KEY"], base_url="https://api.x.ai/v1")
response = client.responses.create(
    model=os.environ["GROK_MODEL"], input="Lis les capacités de carrousel et mes espaces clients.",
    tools=[{"type": "mcp", "server_label": "creatads", "server_url": "https://api.creatads.co/mcp",
            "authorization": os.environ["CREATADS_MCP_TOKEN"],
            "allowed_tools": ["get_carousel_capabilities", "list_clients"]}],
)
```

## ChatGPT : GPT Actions et connecteurs

Pour un GPT personnalisé, importer `https://api.creatads.co/carousels-openapi.json` dans les Actions, puis choisir **API Key → Custom header → X-Api-Key**, avec la clé CreatAds. Le schéma est généré depuis le catalogue MCP et cible l'API existante. La [documentation des GPT Actions](https://help.openai.com/en/articles/9442513-configuring-actions-in-gpts) décrit les restrictions de configuration et de domaine de chaque workspace.

Les connecteurs MCP des interfaces web ChatGPT/Claude qui demandent OAuth nécessitent un parcours OAuth dédié. Notre serveur expose actuellement des clés Bearer ; cette livraison n'ajoute pas OAuth et ne connecte aucun compte automatiquement. Ne pas choisir « sans authentification » pour contourner cette limite. Les exemples API et les schémas ont été préparés ; une connexion de bout en bout dans chaque produit reste à vérifier après déploiement.

## SDK TypeScript

```ts
const carousel = await client.createCarousel(clientId, content, "stable-request-key");
const current = await client.getCarousel(clientId, carousel.id);
const edited = await client.updateCarousel(clientId, carousel.id, current.carousel.version, revisedContent);
const pack = await client.exportCarousel(clientId, edited.id, edited.version);
// pack.archive.url : ZIP privé signé ; pack.slides : PNG dans l'ordre.
```

Le SDK fournit aussi `getCarouselCapabilities`, `listCarousels`, `renderCarouselSlide` et `callTool` pour accéder au parcours de génération existant.

## Veille et structures enregistrées

Les outils suivants partagent la validation et l’isolation `client_id` de l’application. Ils requièrent les migrations `carousel_intelligence`, `carousel_intelligence_safety`, `carousel_intelligence_tiktok_budget` et `carousel_watch_followup_activation`, l’Edge Function `api`, le backend et le worker activé. Découvrir le catalogue actuel avec `tools/list` ; lire `configuration.workerAlive` avant un travail payant. L’accès suit les droits Inspiration du compte.

| Action / outil | Paramètres principaux |
|---|---|
| `get_carousel_intelligence` | `client_id`, `platform` et `niche` facultatifs, `limit` 1–100 |
| `create_carousel_watch` | `client_id`, `idempotency_key` UUID stable, `watch` |
| `set_carousel_watch` | `client_id`, `watch_id`, `enabled`, `confirm: true` pour activer ou augmenter le budget ; `monthly_budget_usd` et `auto_analyze` facultatifs |
| `collect_carousel_watch` | `client_id`, `watch_id`, `idempotency_key` UUID stable, `confirm: true` |
| `get_social_carousel` | `client_id`, `post_id` |
| `analyze_social_carousel` | `client_id`, `post_id`, `language`, `idempotency_key` UUID stable, `confirm: true`, `expected_slide_count` recommandé |
| `get_carousel_patterns` | `client_id`, `platform` et `niche` facultatifs |
| `get_carousel_pattern` | `client_id`, `analysis_id` |

`watch` contient `platform: instagram|tiktok`, `kind: account|search|post`, `target`, `niche` facultatif, `intervalHours: 6|12|24`, `monthlyBudgetUsd: 0.15–100` sur Instagram ou `0.50–100` sur TikTok, `autoAnalyze` et `analysisLanguage: fr|en`. Les valeurs par défaut sont 24 heures, 3 USD, analyse automatique désactivée et français. Utiliser une URL complète pour un post TikTok ; les liens courts de partage ne sont pas résolus. La création ne démarre aucune collecte.

Montrer le budget et obtenir l’accord humain avant `confirm: true`. La collecte réserve 0,15 USD sur Instagram ou 0,50 USD sur TikTok, conformément au minimum de lancement Clockworks. `configuration.collectionReservationByPlatform` expose ces deux montants ; `max_reservation_usd` correspond à la plateforme de la veille. Une analyse réserve 0,07 USD pour 2–8 photos ; au-delà, `(ceil(slides/8)+1) × 0,07 USD`, soit au maximum 0,42 USD. Fournir le nombre de slides inspecté pour refuser une source ayant changé avant confirmation. La source et son relevé sont ensuite figés. Le budget mensuel de la veille, le plafond du compte et le plafond global du worker sont vérifiés atomiquement avant dépense. Un coût inconnu conserve sa réservation. Les opérations d’intelligence ne consomment pas de crédits de génération.

Une collecte/analyse renvoie `{ run, max_reservation_usd, credits_used: 0 }` : c’est un job persistant, pas un résultat immédiat. Lire le tableau de bord et le post pour attendre le résultat. Après un timeout, réutiliser la même clé et lire le job ; ne pas créer une nouvelle clé pour rejouer un lancement incertain. Ce cas suspend la veille. La pause annule le travail non commencé, tandis qu’un run fournisseur connu est encore lu pour récupérer son résultat et son coût.

Les relevés sont datés et append-only. Les remesures à +24 h, +72 h et +7 jours partent du premier relevé réel de chaque période de veille activée, groupées en lots bornés. Les comparaisons demandent cinq autres posts distincts du même compte, plateforme, format et âge comparable. Les métriques absentes restent `null` ; pas de reach, de saves ou de vues de concurrents Instagram inventés. L’analyse vision couvre toutes les photos dans leur ordre, et ses hypothèses restent à tester. Les exemples ordinaires servent de contrôles. Les tendances décrivent l’échantillon observé.

Pour adapter une structure, lire `get_carousel_pattern`, rédiger des textes originaux avec le sujet et le Brand Kit du client, puis `create_carousel` ou `update_carousel`. Ajouter `content.inspiration: { analysis_id, source_hash }` pour garder une référence validée à cette version. Les images et textes source ne sont pas importés. Cette adaptation n’appelle pas Apify.

```ts
const prepared = await client.createCarouselWatch(clientId, {
  platform: "instagram", kind: "account", target: "creator",
  monthlyBudgetUsd: 3, intervalHours: 24, autoAnalyze: false, analysisLanguage: "fr",
}, crypto.randomUUID());
// Après approbation du budget fournisseur par l’utilisateur :
await client.collectCarouselWatch(clientId, prepared.id, crypto.randomUUID(), true);
const dashboard = await client.getCarouselIntelligence(clientId, "instagram");
// Filtre facultatif sur les niches des veilles enregistrées :
const nichePatterns = await client.getCarouselPatterns(clientId, "instagram", "travel");
// Après inspection d’un post et approbation de son coût :
await client.analyzeSocialCarousel(clientId, postId, "fr", crypto.randomUUID(), true, slideCount);
const source = await client.getCarouselPattern(clientId, analysisId);
```

Les SDK proposent aussi `setCarouselWatch`, `getSocialCarousel` et `getCarouselPatterns`. Les mêmes noms d’outils fonctionnent via les configurations MCP Claude, OpenAI et Grok décrites ci-dessus ; adapter la liste `allowed_tools`. Le schéma GPT Actions inclut les huit actions d’intelligence. La présence des outils dans le code n’atteste pas leur déploiement ni la connexion de chaque produit tiers.

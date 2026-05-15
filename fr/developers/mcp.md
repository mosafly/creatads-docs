# MCP Server — Claude Code & Agents IA

Le serveur MCP CreatAds expose l'intégralité de l'API sous forme d'outils, permettant à Claude Code et à tout agent compatible MCP de générer des créatifs publicitaires directement depuis une conversation — sans interface utilisateur.

Le serveur tourne sur `https://api.creatads.co/mcp` (HTTP, JSON-RPC 2.0 sans état). Aucune installation locale requise.

## Configuration

### 1. Obtenir votre clé API

Ouvrez **Paramètres → Clés API** dans l'application et copiez votre clé `cads_...`.

### 2. Ajouter le serveur MCP à Claude Code

```bash
claude mcp add --transport http creatads https://api.creatads.co/mcp \
  --header "Authorization: Bearer YOUR_CADS_KEY"
```

C'est tout. Claude Code découvrira les outils automatiquement.

### 3. Vérifier

Dans une session Claude Code :

```
Use the creatads list_clients tool
```

---

## Outils disponibles

### `list_clients`
Liste tous les espaces clients.
```
Aucun paramètre requis.
```

### `create_client`
Crée un nouvel espace client pour une marque.
```
name (string, requis) — Nom de la marque
```

### `list_campaigns`
Liste toutes les campagnes d'un client.
```
client_id (string, requis)
```

### `create_campaign`
Crée une nouvelle campagne avec un brief.
```
client_id        (string, requis)
name             (string, requis)
cta_text         (string) — ex. "Découvrir", "Shop now"
offer_text       (string) — ex. "-20% ce weekend"
aspect_ratio     (string) — séparé par virgules : "1:1", "4:5", "9:16", "1.91:1"
volume           (number) — total de créatifs à générer (1–60)
selected_angle_ids  (string[]) — IDs de profils issus de list_angles
landing_url      (string)
```

### `generate_creatives`
Déclenche la génération IA pour une campagne (fire-and-forget).
```
campaign_id  (string, requis)
```
La génération tourne en arrière-plan. Appelez `list_creatives` pour suivre la progression.

### `list_creatives`
Liste les créatifs générés pour une campagne.
```
campaign_id (string, requis)
```

### `list_angles`
Liste les angles créatifs d'un client.
```
client_id (string, requis)
```

### `get_angle`
Obtient le détail complet d'un profil.
```
angle_id (string, requis)
```

### `generate_angles`
Génère 10 personas d'audience IA depuis une description de marque.
```
client_id         (string, requis)
research_summary  (string, requis) — description de la marque/produit
language          (string) — "fr" (défaut) ou "en"
```

### `get_brand_kit`
Obtient le brand kit (couleurs, nom, description) d'un client.
```
client_id (string, requis)
```

---

## Exemple de workflow agent

Collez ce prompt dans Claude Code pour générer une campagne complète de manière autonome :

```
I need to launch a campaign for my client Bobotcho (bidet brand).
Use the creatads tools to:
1. List the existing profiles and pick the 2 most relevant for urban eco-conscious buyers
2. Create a campaign called "Été 2026" with CTA "Découvrir", offer "Livraison offerte",
   formats 1:1 and 9:16, volume 4
3. Generate the creatives, then poll list_creatives until images are ready and return the URLs
```

Claude appellera de manière autonome `list_angles` → `create_campaign` → `generate_creatives` → `list_creatives` et retournera les URLs.

---

## Séquence d'appels typique

```
1. list_clients                          → obtenir le client_id
2. list_angles(client_id)              → obtenir les IDs de profils
   └─ ou generate_angles(...)         → s'il n'y a pas encore de profils
3. create_campaign(client_id, ...)       → obtenir le campaign_id
4. generate_creatives(campaign_id)       → démarre la génération en arrière-plan
5. list_creatives(campaign_id)           → interroger jusqu'à l'apparition des images
```

---

## Messages d'erreur

| Situation | Message |
|---|---|
| Clé manquante ou invalide | `Invalid or revoked API key` |
| Pas d'abonnement | `Premium subscription required. Upgrade at https://creatads.co/pricing` |
| Paramètre manquant | `Missing required parameter: <name>` |

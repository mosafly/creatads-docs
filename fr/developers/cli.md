# Référence CLI

## Installation

```bash
npm install -g creatads-cli
```

## Options globales

```bash
creatads --version   # 1.0.0
creatads --help
creatads <command> --help
```

---

## auth

Gère les identifiants d'authentification stockés dans `~/.creatads/config.json`.

### `auth login`
Configurez votre clé API de manière interactive.
```bash
creatads auth login
# Paste your API key (cads_...) when prompted
```

### `auth whoami`
Affiche le préfixe de la clé configurée et l'URL API.
```bash
creatads auth whoami
# Base URL: https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api
# Key prefix: cads_2ad912d…
```

### `auth logout`
Supprime les identifiants stockés.
```bash
creatads auth logout
```

---

## clients

Gérez les espaces clients. Chaque client est une marque ou un annonceur.

### `clients list`
```bash
creatads clients list
# ┌──────────────────────────────────────┬──────────┬─────────┬─────────────┐
# │ ID                                   │ Name     │ Default │ Created     │
# ├──────────────────────────────────────┼──────────┼─────────┼─────────────┤
# │ 89b18b5f-b3a8-4cf6-9b47-beafb9956b04 │ Bobotcho │ ✓       │ 4 mars 2026 │
# └──────────────────────────────────────┴──────────┴─────────┴─────────────┘
```

### `clients create <name>`
```bash
creatads clients create "Nike France"
```

### `clients use <id>`
Définit un client par défaut — évite de passer `--client` à chaque commande suivante.
```bash
creatads clients use 89b18b5f-b3a8-4cf6-9b47-beafb9956b04
# Default client set to: Bobotcho (89b18b5f-...)
```

Après ça, toutes les options `--client` deviennent optionnelles.

---

## campaigns

### `campaigns list`
```bash
creatads campaigns list
creatads campaigns list --client <id>   # override default

# ┌──────────────────────────────────────┬────────────┬────────────┬────────┬────────────┐
# │ ID                                   │ Name       │ Status     │ Volume │ Created    │
# └──────────────────────────────────────┴────────────┴────────────┴────────┴────────────┘
```

### `campaigns create`
```bash
creatads campaigns create \
  --name "Summer Sale" \
  --cta "Découvrir" \
  --offer "-20% ce weekend" \
  --aspect-ratio "1:1,9:16" \
  --volume 6 \
  --landing-url "https://example.com/sale"

# Options :
#   --client <id>          ID client (utilise le défaut si défini)
#   --name <n>             Nom de la campagne (requis)
#   --cta <text>           Texte du bouton CTA
#   --offer <text>         Texte de l'offre/promo
#   --aspect-ratio <ratio> Séparé par virgules : 1:1, 4:5, 9:16, 1.91:1 (défaut : 1:1)
#   --volume <n>           Total de créatifs à générer (défaut : 1)
#   --landing-url <url>    URL de destination
```

### `campaigns generate <campaign-id>`
Lance la génération et attend les résultats (~2–5 min).
```bash
creatads campaigns generate 03d75c13-a9a2-40a0-88b4-acf7a2e702f9

# ⠸ Generating creatives…
# ✔ Done — 3 creative(s) generated
#
# ┌──────────────────────────────────────┬───────────────┬──────────────┬────────────┐
# │ ID                                   │ Image URL     │ Aspect Ratio │ Created    │
# └──────────────────────────────────────┴───────────────┴──────────────┴────────────┘
```

**Options :**
```bash
--no-wait           Fire-and-forget (retourne le job_id immédiatement)
--timeout <seconds> Délai d'expiration de la génération en secondes (défaut : 180)
```

---

## creatives

### `creatives list <campaign-id>`
```bash
creatads creatives list 03d75c13-a9a2-40a0-88b4-acf7a2e702f9
```

---

## profiles

Personas d'audience utilisées pour cibler les campagnes.

### `profiles list`
```bash
creatads angles list
creatads angles list --client <id>
```

### `profiles get <id>`
Affiche le détail complet d'un profil.
```bash
creatads angles get c4764793-527f-4afc-8126-e0a3a34cfe7d

# Seniors (65+) & Retirees in Abidjan
# ────────────────────────────────────────────────────────────
# ID          c4764793-527f-4afc-8126-e0a3a34cfe7d
# Client ID   89b18b5f-b3a8-4cf6-9b47-beafb9956b04
# Créé le     4 mars 2026
#
# Pain Point
#   Loss of dignity and fear of being a burden...
#
# Angle
#   The "Dignity & Independence" approach: Focus on maintaining privacy...
```

### `profiles generate`
Génère 10 angles IA depuis une description de marque ou produit.
```bash
creatads angles generate \
  --summary "French DTC brand selling minimalist leather wallets to urban professionals aged 25-40, focusing on quality and sustainability" \
  --language fr

# Options :
#   --client <id>        ID client (utilise le défaut si défini)
#   --summary <text>     Description de la marque/produit (requis, plus c'est riche, meilleurs sont les profils)
#   --language <lang>    fr (défaut) ou en
```

---

## keys

Gérez les clés API de votre compte.

### `keys list`
```bash
creatads keys list
```

### `keys create`
```bash
creatads keys create --name "Serveur de production"
# ✔ Key created: cads_xxxx… (copy now — shown only once)
```

### `keys revoke <id>`
```bash
creatads keys revoke <key-id>
```

---

## Fichier de configuration

Stocké dans `~/.creatads/config.json` (chmod 600) :

```json
{
  "api_key": "cads_...",
  "base_url": "https://bgpaitczhnfsqkukkwqi.supabase.co/functions/v1/api",
  "default_client_id": "89b18b5f-b3a8-4cf6-9b47-beafb9956b04"
}
```

Vous pouvez modifier ce fichier directement pour pointer vers un endpoint API différent (ex. développement local).

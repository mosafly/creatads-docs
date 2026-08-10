# Référence CLI

Le package `creatads-cli` 1.0.0 vit dans `services/cli`. Construisez-le depuis le monorepo, ou installez-le depuis un registre uniquement lorsqu'une publication est disponible.

## Commandes

```bash
creatads auth login
creatads auth whoami
creatads auth logout

creatads clients list
creatads clients create "Maison Leon"
creatads clients use <client-id>

creatads angles list [--client <id>]
creatads angles get <angle-id>
creatads angles generate [--client <id>] --summary "..." [--language fr|en]

creatads campaigns list [--client <id>]
creatads campaigns create \
  [--client <id>] --name "Promotion été" \
  [--cta "Découvrir"] [--offer "-20 %"] \
  [--aspect-ratio "1:1,9:16"] [--volume 4] \
  [--landing-url "https://example.com"]
creatads campaigns generate <campaign-id> [--no-wait] [--timeout 180]

creatads creatives list <campaign-id>
```

Les anciennes commandes `profiles` n'existent plus. Le CLI de création de campagne n'expose pas encore les IDs d'angles, URLs d'images ou plateformes, bien que l'API et le SDK les acceptent.

Sans `--no-wait`, le CLI retourne dès qu'au moins une créative est disponible ou à expiration du délai. Pour un lot, vérifiez le nombre final avec `creatads creatives list`.

## Clés API

```bash
creatads keys list [--jwt <supabase-session-jwt>]
creatads keys create [--name "Production"] [--jwt <supabase-session-jwt>]
creatads keys revoke <key-id> [--jwt <supabase-session-jwt>]
```

Ces commandes exigent un JWT Supabase et le demandent s'il est omis.

La configuration locale est stockée dans `~/.creatads/config.json` avec la clé, l'URL API et l'espace client par défaut. Ne la commitez pas.

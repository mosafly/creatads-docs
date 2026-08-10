# Plateforme développeur CreatAds

> Contrat vérifié dans le code le 10 juillet 2026.

CreatAds expose la génération de campagnes via une API REST fondée sur des actions et plusieurs clients construits au-dessus.

| Interface | État actuel | Usage |
|---|---|---|
| API REST | Endpoint de production | Toute intégration HTTP |
| SDK TypeScript | Package privé du monorepo | Intégrations Node.js internes |
| CLI | Package du monorepo | Terminal et scripts |
| MCP distant | `https://api.creatads.co/mcp` | Agents compatibles MCP |
| MCP local | Package stdio du monorepo | Agents locaux utilisant la configuration CLI |

Ces interfaces couvrent les espaces clients, campagnes, créatives, angles et Brand Kit. Inspiration, Explorer, la recherche Ads Library, Clone et la matrice complète de Mode Pro restent réservés à l'application.

## Prérequis

- un compte [creatads.co](https://creatads.co) ;
- une clé créée dans **Paramètres > API** ;
- un plan incluant l'accès développeur. L'offre publique le documente à partir de Fondateur.

Note d'implémentation : le contrôle REST accepte actuellement tout produit Stripe payant actif, y compris Solo, alors que l'interface masque les clés API pour Solo. À l'inverse, la table de fonctionnalités de l'interface inclut Beta, mais le contrôle REST ne reconnaît pas le rôle bêta comme un abonnement payant. Cette politique produit/code reste à trancher.

Les crédits sont partagés avec l'application : une image générée consomme un crédit.

| Plan | Crédits image mensuels |
|---|---:|
| Starter | 3 |
| Beta | 15 pendant l'accès bêta |
| Solo | 30 |
| Fondateur | 80 |
| Croissance | 350 |
| Agence | Illimité |

## Démarrage rapide

```bash
creatads auth login
creatads clients list
creatads clients use <client-id>
creatads angles generate \
  --summary "Marque de soins premium pour femmes urbaines de 25 à 40 ans" \
  --language fr
creatads campaigns create \
  --name "Promotion été" \
  --cta "Découvrir" \
  --offer "-20 % ce week-end" \
  --aspect-ratio "1:1,9:16" \
  --volume 4
creatads campaigns generate <campaign-id>
```

## Documentation

- [Authentification](./authentication.md)
- [API REST](./api.md)
- [CLI](./cli.md)
- [SDK TypeScript](./sdk.md)
- [MCP](./mcp.md)
- [Workflows et limites](./workflows.md)

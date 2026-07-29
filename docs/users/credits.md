# Crédits et abonnement

## Plans

| Plan | Prix mensuel | Générations | Accès principal |
|---|---:|---:|---|
| Starter | 0 € | 3 | Mode Facile, teaser Inspiration |
| Beta Tester | 0 € | 15 | Accès temporaire aux fonctions premium |
| Solo | 19 € | 30 | Mode Facile, Mode Pro, Brand Kit, Ads Library |
| Fondateur | 49 € | 80 | Tout Solo + API, MCP/CLI et Meta Ads |
| Croissance | 149 € | 350 | Volumes élevés et espaces clients illimités |
| Agence | 299 € | illimité | Production à fort volume |

Les labels et quotas proviennent de `src/lib/plans.ts` et de `supabase/functions/_shared/plan-quotas.ts`.

## Consommation

**1 image générée = 1 crédit.**

- Mode Facile : une variante consomme un crédit.
- Presets : un crédit par preset généré.
- Mode Pro : un crédit par cellule du produit cartésien.
- API et MCP : un crédit par image générée.
- Analyse créative, lecture, création de campagne et génération d’angles : aucun crédit image.

L’usage est compté par mois calendaire UTC. Il ne s’agit pas de la date anniversaire de la souscription.

Des crédits top-up peuvent être attribués par l’administration. Ils s’ajoutent au quota mensuel et persistent jusqu’à leur consommation.

## Quotas Inspiration

Les recherches Ads Library utilisent un quota séparé :

| Plan | Recherches manuelles/mois |
|---|---:|
| Starter | 0 |
| Beta Tester | 15 |
| Solo | 20 |
| Fondateur | 75 |
| Croissance | 250 |
| Agence | 1000 |

Explorer, les résultats servis depuis le cache et les aperçus concurrents de l’onboarding ne consomment pas ce quota manuel.

## Accès par plan

- Starter voit un rail Inspiration limité à neuf cartes mais pas la page Ads Library complète.
- Mode Pro, Brand Kit et Ads Library complète commencent à Solo.
- La création de clés API, le lien MCP/CLI et Meta Ads sont exposés à partir de Fondateur dans l’offre payante normale.
- L’accès Beta est temporaire et peut différer des contrôles serveur de l’API développeur.

## Gérer l’abonnement

Dans **Paramètres → Abonnement**, vous pouvez consulter l’usage, changer de plan, ouvrir le Customer Portal Stripe, télécharger des factures ou annuler.

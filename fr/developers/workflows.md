# Workflows développeur et limites

## Préparer un espace client

```bash
creatads clients create "Maison Leon"
creatads clients use <client-id>
creatads angles generate \
  --summary "Chocolaterie artisanale française, premium, cadeau, fabrication bean-to-bar" \
  --language fr
creatads angles list
```

La génération crée dix angles réutilisables sans consommer de crédit image.

## Générer une campagne

```bash
creatads campaigns create \
  --name "Offre week-end" \
  --cta "Découvrir" \
  --offer "Livraison offerte" \
  --aspect-ratio "1:1,9:16" \
  --volume 4 \
  --landing-url "https://example.com/week-end"

creatads campaigns generate <campaign-id> --no-wait
creatads creatives list <campaign-id>
```

Chaque image réussie consomme un crédit. Le lot entier est refusé s'il dépasse le quota restant.

## Suivre un lot avec le SDK

```typescript
await creatads.generateCreatives(campaign.id);

let creatives = [];
while (creatives.length < 4) {
  await new Promise((resolve) => setTimeout(resolve, 15_000));
  creatives = await creatads.listCreatives(campaign.id);
}
```

Utilisez une boucle fondée sur le nombre attendu lorsque le lot complet est important : le helper SDK retourne dès la première image.

## Comportement du volume

L'API calcule `combinaisons x max(1, floor(volume / combinaisons))`, où les combinaisons correspondent aux angles sélectionnés (ou un angle vide) multipliés par les ratios. Pour un total exact, choisissez un volume au moins égal et divisible par `angles x ratios` ; sinon le champ `jobs` retourné peut différer du volume demandé.

Ce contrat diffère de Mode Pro dans l'application, qui combine ratios, modes/scènes, angles et volume. Les presets neutralisent le volume à un et les lots supérieurs à 60 demandent une confirmation sans limite dure.

Inspiration, Explorer, la recherche Ads Library, Clone, les cinq presets publicitaires, Favoris et Winners ne sont pas automatisables via les interfaces développeur actuelles.

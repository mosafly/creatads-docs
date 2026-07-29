# Mode Pro

Mode Pro (`/campaigns`) génère des lots multi-formats et multi-angles à partir d’une configuration unique.

## Construire un lot

Sélectionnez :

- une image de référence et/ou une image produit ;
- un ou plusieurs ratios ;
- un ou plusieurs modes ;
- zéro, un ou plusieurs angles ;
- des presets de scène éventuels ;
- un volume ;
- la langue de sortie ;
- le Brand Kit, le CTA et l’offre.

Le total suit la formule :

```text
ratios × scènes/modes × angles × volume
```

Lorsqu’au moins un preset est sélectionné, le multiplicateur de volume passe automatiquement à 1 : chaque preset représente déjà une scène distincte.

Au-delà de 60 images, CreatAds demande une confirmation. Cette confirmation est un garde-fou, pas une limite dure.

## Ratios

- `1:1`
- `4:5`
- `9:16`
- `16:9`

## Les 13 modes

| Famille | Modes |
|---|---|
| Styles | Studio, Lifestyle, UGC |
| Layouts | Avant/Après, Feature Callout, Témoignage |
| Formats publicitaires | Us vs Them, Anti-marketing, UGC statique, Témoignage, Offre limitée |
| Transformations | Reproduire, Reformater, Éditer |

Témoignage apparaît à la fois comme layout et comme format publicitaire, mais correspond au même identifiant `testimonial-overlay`.

## Champs spécifiques

- **Feature Callout** : jusqu’à cinq bénéfices.
- **Témoignage** : texte, auteur et note.
- **Avant/Après** : la référence représente l’avant et le produit/la seconde image l’après.
- **Us vs Them, Anti-marketing, UGC statique** : headline/hook principal.
- **Offre limitée** : texte d’offre.
- **Reproduire** : référence obligatoire pour un résultat cohérent.
- **Éditer** : modification ciblée d’une créative existante.

## Exécution et reprise

- Quatre images sont générées en parallèle.
- La progression est conservée dans le navigateur.
- Un lot reste récupérable pendant environ dix minutes.
- Après un rechargement, un lot sans activité depuis environ une minute peut être repris automatiquement.

La reprise repose sur `localStorage`. Elle n’est pas garantie après nettoyage du stockage du navigateur, changement de navigateur ou expiration du TTL.

## Résultats

Les créatives sont enregistrées dans la campagne et peuvent être téléchargées, regroupées, reformattées ou utilisées pour préparer une campagne Meta.

L’analyse créative est déclenchée séparément et ne consomme pas le quota de génération.

## Crédits

Chaque cellule du produit cartésien correspond à une image et consomme un crédit. Vérifiez le total affiché avant de lancer le lot.

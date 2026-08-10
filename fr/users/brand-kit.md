# Brand Kit

Le Brand Kit centralise le contexte et l’identité d’une marque pour un espace client.

## Données enregistrées

- nom et description ;
- couleurs principale, secondaire et accent ;
- polices principale et secondaire ;
- logos clair et sombre ;
- résumé de recherche ;
- niche et mot-clé de niche ;
- marché (`FR` ou `US` dans le workflow Ads Library actuel) ;
- catégorie Explorer ;
- concurrents détectés.

## Analyse depuis une URL

Pendant l’onboarding ou depuis l’action de réanalyse du Brand Kit, CreatAds peut :

1. lire les métadonnées du site ;
2. détecter un logo ou une icône ;
3. extraire une palette lorsque le format du logo le permet ;
4. télécharger et ré-héberger des images produit ;
5. inférer niche, marché, catégorie et concurrents ;
6. générer ou régénérer les angles.

### Limitations

- Les protections anti-bot ou anti-hotlink peuvent empêcher la récupération.
- Les logos SVG ne peuvent pas toujours être analysés par la librairie de couleurs.
- Les métadonnées absentes produisent des valeurs par défaut.
- Vérifiez toujours le résultat avant de l’enregistrer.

## Configuration manuelle

Dans `/brand-setup`, vous pouvez corriger chaque champ, téléverser des logos et importer un document de recherche.

L’import documentaire accepte les formats pris en charge par `parse-document` et remplit le résumé de recherche. Il ne remplace pas automatiquement toutes les propriétés du Brand Kit.

## Utilisation

- Mode Facile et Mode Pro peuvent activer le Brand Kit.
- Le logo final est appliqué de manière déterministe par le pipeline de composition lorsque les conditions du mode le permettent.
- En Clone, la marque source est interdite et la marque utilisateur est réinjectée.

Chaque espace client possède son propre Brand Kit.

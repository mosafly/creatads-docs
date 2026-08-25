# Organiser, analyser et diffuser

## Bibliothèque

`/library` regroupe les ressources de l’espace client actif : images générées, références, produits et templates. Vous pouvez importer, prévisualiser, parcourir la lightbox au clavier, télécharger et supprimer. L’ancienne route `/templates` redirige vers la section Templates de la bibliothèque.

## Analyse créative

`/analyze` téléverse une créative et produit un score global, un statut Meta-Ready, des catégories commentées, des points forts/faibles et des recommandations. Les résultats sont enregistrés dans l’historique ; l’image peut être sauvegardée comme template ou envoyée dans Mode Pro comme référence.

L’analyse ne consomme pas de crédit image. Ses scores sont heuristiques et ne prédisent pas le ROAS, le CPA ou les performances réelles.

## Board et calendrier

- `/campaigns/board` affiche les campagnes en colonnes Brouillon, Génération et Terminée. Il s’agit d’une vue de suivi, pas d’un Kanban modifiable.
- `/campaigns/calendar` enregistre la date et les plateformes d’une campagne en base.
- Les placements manuels de créatives et les notes restent dans `localStorage` : ils ne sont pas synchronisés entre navigateurs ou appareils.

## Meta Ads

`/campaigns/meta-ads` est la page **Analytics Meta**. Connectez un compte publicitaire, puis actualisez les données des sept derniers jours. Ouvrez une campagne pour voir ses annonces, leurs dépenses, impressions, clics, CTR, CPM et couverture. Une annonce peut être liée à une créa Creatads lorsque l'identifiant Meta est connu.

La page est strictement en lecture seule : elle ne crée ni ne modifie une campagne, audience, annonce, budget ou statut de diffusion. Elle exige les identifiants Meta configurés côté service et un plan autorisé : Beta, Fondateur, Croissance, Agence ou Admin dans la table de fonctionnalités actuelle.

CreatAds ne garantit ni validation par Meta ni performance. Vérifiez toujours les données finales dans Meta Ads Manager.

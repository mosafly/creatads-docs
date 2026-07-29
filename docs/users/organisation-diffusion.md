# Organiser, analyser et diffuser

## Bibliothèque

La page `/library` regroupe les ressources de l’espace client actif.

- **Mes créas** : toutes les images, puis filtres Générées, Références, Produits et Templates.
- **Templates** : catalogue de templates, également accessible via l’ancienne route `/templates` qui redirige vers `/library?tab=templates`.
- **Actions** : importer des images, prévisualiser, naviguer au clavier dans la lightbox, télécharger et supprimer.

Une ressource importée ou générée appartient à l’espace client actif. Vérifiez cet espace avant toute action.

## Analyse créative

La page `/analyze` permet de téléverser une créative puis d’appeler l’analyse IA. Le résultat comprend :

- un score global et un statut Meta-Ready ;
- des scores et commentaires par catégorie, dont conformité Meta et potentiel de conversion ;
- des points forts, faiblesses et recommandations ;
- une analyse des angles et segments quand ils sont disponibles.

Les analyses sont enregistrées dans l’historique. Une image analysée peut être sauvegardée comme template ou envoyée vers Mode Pro comme référence. L’analyse ne consomme pas de crédit image.

Les scores sont des recommandations heuristiques. Ils ne prédisent pas le ROAS, le CPA ou les résultats réels d’une campagne.

## Board et calendrier

- `/campaigns/board` répartit les campagnes en colonnes Brouillon, Génération et Terminée. Le board est actuellement une vue de suivi, pas un Kanban modifiable par glisser-déposer.
- `/campaigns/calendar` permet de planifier une campagne et ses plateformes. La date et les plateformes de campagne sont enregistrées en base.
- Les placements manuels de créatives et les notes du calendrier sont stockés dans `localStorage`, car aucune table backend dédiée n’existe encore. Ils ne sont donc pas synchronisés entre navigateurs ou appareils.

## Préparer et publier sur Meta Ads

La page `/campaigns/meta-ads` n’affiche que les campagnes terminées.

Workflow :

1. ouvrez une campagne terminée et chargez ses créatives ;
2. lancez la préparation IA du texte et du ciblage ;
3. vérifiez ou modifiez headline, texte principal, description, CTA, offre, URL et budget ;
4. sélectionnez les créatives à publier ;
5. choisissez la stratégie et l’objectif Meta ;
6. publiez, puis consultez les insights disponibles.

La gestion des audiences permet aussi de créer sur Meta des audiences sélectionnées. La publication exige les identifiants Meta configurés côté service et un plan autorisé : Beta, Fondateur, Croissance, Agence ou Admin dans la table de fonctionnalités actuelle.

CreatAds prépare et envoie la campagne, mais ne garantit ni validation par Meta ni performance publicitaire. Vérifiez toujours le budget, l’URL, les textes, les ciblages et l’état final dans Meta Ads Manager.

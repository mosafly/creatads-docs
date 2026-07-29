# Inspiration, Explorer et Clone

CreatAds propose trois niveaux de découverte publicitaire.

## Rail Inspiration

Le rail est visible en haut de Mode Facile et Mode Pro.

- Starter : jusqu’à 9 inspirations.
- Autres plans : jusqu’à 24 par défaut.
- Les publicités de votre propre marque sont filtrées.
- « Voir tout » ouvre la page Ads Library.
- « Mes créas » affiche vos productions récentes.
- Favoris et Winners sont encore des fonctions à venir.

## Explorer

Explorer lit une bibliothèque partagée de publicités Meta curées et classées. La consultation ne lance pas un scrape Apify.

Filtres disponibles :

- catégorie ;
- marché FR/US ;
- format créatif.

Quand les données de transparence UE existent, les cartes peuvent afficher la portée UE et un percentile « Top X % ». Ces données ne sont pas présentes sur toutes les publicités.

## Concurrents

Les concurrents sont détectés pendant l’analyse de la marque. Un onglet par concurrent peut lancer une recherche exacte.

Les aperçus concurrents de l’onboarding sont ouverts à tous les plans et utilisent un mot-clé choisi côté serveur. Ils ne consomment pas le quota de recherche manuelle.

## Recherche

La recherche manuelle interroge les publicités par mot-clé, pays et statut. Elle est disponible sur la page Ads Library pour les plans autorisés.

- Un cache valide est gratuit.
- Une nouvelle recherche débite le quota Ads Library.
- Si le scrape échoue ou qu’un run identique est déjà en cours, le crédit de recherche est remboursé.

## Score

Le score est un classement heuristique basé sur les signaux accessibles : durée de diffusion, activité, variantes et autres métadonnées. Il ne représente pas le ROAS, le CPA ou le chiffre d’affaires de l’annonceur.

## Cloner une inspiration

1. Cliquez sur la baguette de la carte.
2. CreatAds ré-héberge l’image avec `save-template`.
3. Mode Pro peut analyser le texte pour proposer CTA et offre.
4. Le générateur passe en mode Reproduire.
5. La marque source est interdite dans le prompt final.

Le résultat reprend la structure et la hiérarchie de la référence, mais il reste une nouvelle génération. Le rendu n’est pas garanti pixel-identique.

## Limitations

- Images statiques uniquement.
- Certaines URLs Meta peuvent expirer ou bloquer le hotlinking.
- Les données UE et la classification de format ne sont pas toujours disponibles.
- Explorer, Recherche et Clone ne sont pas exposés par l’API publique, le SDK, le CLI ou le MCP.

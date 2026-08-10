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

La sélection V2 s'appelle **« À tester pour ta marque »**. Ses 12 premières positions mélangent quatre publicités aux preuves les plus solides, quatre pistes très pertinentes pour la marque, deux nouveautés et deux explorations. Un même annonceur ne peut pas occuper plus de deux de ces positions.

Le déploiement est progressif : V2 est d'abord calculé en parallèle sans modifier l'ordre visible, puis activé sur le rail avant d'être généralisé à Explorer, Concurrents et Recherche.

## Explorer

Explorer lit une bibliothèque partagée de publicités Meta curées et classées. La consultation ne lance pas un scrape Apify.

Filtres disponibles :

- catégorie ;
- marché FR/US ;
- format créatif.

Quand les données de transparence UE existent et que la cohorte comparable contient au moins 30 publicités, les cartes peuvent afficher un percentile « Top X % de portée ». Ces données ne sont pas présentes sur toutes les publicités.

Le classement commence par le marché, la catégorie et le format exacts. S'il reste moins de 12 publicités éligibles, CreatAds élargit successivement le format, la catégorie puis le marché et l'indique au-dessus des résultats. Le classement V2 utilise une cohorte complète indépendante de l'ancien score V1.

## Concurrents

L’analyse de marque peut proposer des **candidates**. Elles restent indiquées comme « À vérifier » : une suggestion IA n’est pas automatiquement un concurrent.

Depuis Inspiration, vous pouvez confirmer, rejeter ou ajouter une marque manuellement. L’ajout manuel accepte un nom, un domaine, une page Meta et des alias. Il crée une candidate à vérifier et ne lance jamais une collecte tout seul.

Après confirmation, « Voir ses publicités » ouvre un préflight : marché, fraîcheur du cache, quota de veille et mention de la source Meta. Un cache frais ouvre les résultats immédiatement, sans coût ni consentement supplémentaire. Sinon, CreatAds ne lance une veille qu’après l’action explicite « Lancer la veille ».

L’onboarding peut afficher les candidates détectées, mais il renvoie vers Inspiration pour leur validation et ce préflight. Il ne déclenche pas de scrape Apify.

## Recherche

La recherche manuelle interroge les publicités par mot-clé, pays et statut. Elle est disponible sur la page Ads Library pour les plans autorisés.

- Un cache valide est gratuit.
- Une nouvelle recherche débite le quota Ads Library.
- Si le scrape échoue, le quota est remboursé. Si une collecte identique est déjà en cours, vous rejoignez cette collecte sans en démarrer une seconde.

## Classement et labels

Le classement V2 combine quatre dimensions : signaux de performance publics, pertinence pour votre marque, fraîcheur et facilité de réutilisation comme inspiration. Le niveau de confiance reste séparé du classement : une donnée absente ne renforce jamais artificiellement les autres signaux.

CreatAds n'affiche pas « Winner » sur une publicité concurrente. Selon les preuves réellement disponibles, une carte peut indiquer :

- **Signal fort** ;
- **Top X % de portée** ;
- **Diffuse depuis N jours** ;
- **Nouveau** ;
- **À surveiller** ;
- **Données limitées**.

Ces labels ne représentent jamais le ROAS, le CTR, le CPA ou le chiffre d'affaires de l'annonceur.

Le détail d'une publicité explique ce que CreatAds observe, pourquoi la publicité est pertinente pour votre marque et quelles preuves restent absentes. Vous pouvez l'enregistrer, la masquer ou l'utiliser comme inspiration.

Les actions fortes, comme enregistrer, utiliser puis télécharger une création générée, améliorent progressivement la pertinence du feed. L'action **Réinitialiser mes préférences** supprime les éléments enregistrés ou masqués et ignore l'historique comportemental antérieur pour le classement futur.

## Cloner une inspiration

1. Cliquez sur la baguette de la carte.
2. CreatAds ré-héberge l’image avec `save-template`.
3. Mode Pro peut analyser le texte pour proposer CTA et offre.
4. Le générateur passe en mode Reproduire.
5. La marque source est interdite dans le prompt final.

Le résultat reprend la structure et la hiérarchie de la référence, mais il reste une nouvelle génération. Le rendu n’est pas garanti pixel-identique.

Ouvrir, enregistrer ou préremplir une inspiration ne consomme aucun crédit de génération. Les crédits sont calculés uniquement au lancement de la génération.

## Limitations

- Images statiques uniquement.
- Certaines URLs Meta peuvent expirer ou bloquer le hotlinking.
- Les données UE et la classification de format ne sont pas toujours disponibles.
- Explorer, Recherche et Clone ne sont pas exposés par l’API publique, le SDK, le CLI ou le MCP.

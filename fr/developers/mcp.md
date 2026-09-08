# Intégration MCP et plugin Codex

Endpoint : `https://api.creatads.co/mcp`. Le contrat V3 est livré avec la migration `agentic_creative_workflow` et le service imgproc correspondant. Vérifiez toujours `get_generation_capabilities` et `tools/list` sur votre instance : des fichiers locaux ne prouvent pas qu'un déploiement est actif.

## Installation Codex

Le [dépôt public des intégrations](https://github.com/mosafly/creatads-integrations) contient le plugin et sept skills, sans code serveur ni données client. Ajoutez son répertoire comme marketplace Codex, puis installez `creatads@creatads-public`.

La configuration HTTP référence le **nom** de variable `CREATADS_MCP_TOKEN`. Définissez sa valeur dans votre environnement secret local, jamais dans le manifeste ou la conversation. Redémarrez Codex si son environnement a changé. Ouvrez une nouvelle tâche après l'installation pour charger les skills.

Le serveur stdio `services/mcp` transmet désormais le catalogue et les appels au même endpoint HTTP. Il utilise cette variable ou la configuration du CLI CreatAds. Les deux transports partagent donc les mêmes règles et outils.

## Choisir le bon parcours

| Skill | Usage |
|---|---|
| creatads-guide | Découvrir et choisir les fonctionnalités pertinentes |
| creatads-brand | Brand Kit, faits produit et références validées |
| creatads-inspiration | Explorer, rechercher, favoris et templates |
| creatads-create | Générer avec Mode Facile ou Mode Pro |
| creatads-edit | Corriger ou reformater une image existante |
| creatads-review | Fidélité produit et analyse marketing distinctes |
| creatads-library | Retrouver et afficher les images |

L'agent consulte le contexte existant avant de poser des questions. Il propose des choix pertinents, sans imposer un preset, un angle, une offre ou un CTA à chaque visuel éditorial.

## Contrat de génération

Les modes et les 27 presets proviennent du même catalogue que l'application. Un mode définit l'opération, un preset la scène, un angle le message et un ratio la forme de l'image.

Mode Facile produit 1 à 3 images et accepte les ratios retournés par `easy_mode_aspect_ratios`. Mode Pro accepte les cinq ratios et deux formes **exclusives** :

- `items` : un objet par image, pour cinq concepts distincts par exemple ;
- matrice `modes × aspect_ratios × angle_ids × repetitions`, pour tester volontairement des combinaisons. Les presets remplacent les scènes et neutralisent les répétitions.

Maximum 60 images par lot. Une combinaison invalide est rejetée plutôt que remplacée silencieusement par un défaut.

`preview_easy_generation` ou `preview_pro_batch` enregistre le plan et retourne : `preview_id`, `session_id`, nombre exact d'images, crédits estimés, budget initial/corrections et détail de chaque image, dont `effective_prompt`, références, produit/version, mode, preset, ratio, angle et copy.

Une preview ne génère pas d'image et ne consomme pas de crédit. Après validation du plan et du budget par l'utilisateur, appeler le `generate_*` correspondant avec `confirm: true` et une `idempotency_key` stable. Reprendre les mêmes identifiants après un timeout.

## Faits et fidélité produit

`list_product_briefs`, `get_product_brief` et `upsert_product_brief` gèrent des fiches versionnées par client. Elles contiennent une description factuelle, des allégations avec sources, des interdictions, des éléments à préserver et des références dont le rôle est explicite : produit, emballage, installation, détail, logo, style ou créa source.

`validated: true` signifie que l'utilisateur a vérifié ces informations. Une modification exige `expected_version`; un conflit ne doit jamais écraser la version plus récente. La preview conserve un instantané : un changement ultérieur du Brand Kit ou de la fiche ne modifie pas son prompt.

`edit` exige une source et une instruction dans `variation_prompt` ou `prompt_override`, jamais les deux. `edit` et `reformat` refusent les champs de copy automatique, presets, angles ou Brand Kit qui seraient ignorés. Pour changer le texte dans une image existante, écrire la substitution exacte dans `variation_prompt`. Pour une nouvelle publicité, choisir un mode de création.

Avis, étoiles, promotions et transformations ne doivent pas être inventés. Le preset `multi-mini` est signalé indisponible tant que le contrat ne reçoit pas plusieurs avis sourcés.

## Budgets, reprise et annulation

Un crédit est consommé par image techniquement réussie, **même si le contrôle visuel la rejette**. Le budget de correction est 0 par défaut et doit être approuvé explicitement. Une correction emploie `session_id`, `correction: true` et le `parent_creative_id` issu de cette session. Les versions précédentes sont conservées.

L'activation réserve les crédits atomiquement pour les lots MCP. `get_quota_status` distingue utilisés, réservés et disponibles. Les identifiants fournisseur et baux de travail sont persistés pour reprendre une exécution après redémarrage. Une acceptation fournisseur incertaine reste réservée et nécessite une réconciliation : aucune nouvelle soumission automatique.

Cette garantie couvre les lots MCP. Les nouvelles demandes de l'application tiennent compte des réservations, mais une génération classique déjà en cours n'est pas un slot MCP : ce n'est pas un verrou global sur tous les points d'entrée concurrents.

`get_batch_status` retourne les résultats par image et peut reprendre le travail déjà autorisé. `resume_batch` ne relance que les échecs identifiés dans le budget restant. `cancel_batch` annule le travail en attente ; les images déjà soumises peuvent finir et être facturées.

## Inspiration, marque et affichage

`extract_brand` retourne des suggestions sans écraser le kit ; `update_brand_kit` applique les champs approuvés uniquement. `list_inspiration` consulte le pool existant, `search_inspiration` réutilise le service et ses quotas, `get_inspiration_ad` inspecte un résultat, et les outils de favoris/templates enregistrent uniquement les choix demandés. Une recherche peut consommer un quota de recherche, distinct des crédits image. Aucun suivi concurrent automatique n'est créé.

`get_creative` avec `include_image: true` retourne un bloc image MCP natif, un lien ressource et des données structurées, avec un bloc JSON texte pour compatibilité. L'affichage dépend du client MCP. Codex privilégie l'image native, puis un aperçu autorisé ou un lien utilisable.

`validate_creative` contrôle fidélité/dimensions/copy avec trois états : `passed`, `failed`, `uncertain`. Une preuve manquante n'est pas un succès. `analyze_creative` évalue séparément le potentiel marketing ; son score ne prouve ni fidélité, ni ventes, ni approbation Meta.

## Compatibilité et limites

Les anciens outils de campagne restent disponibles pour les intégrations existantes, mais les nouveaux parcours doivent employer preview/confirmation. Une review ou une récupération n'autorise pas de génération supplémentaire. Publication Meta, dépenses publicitaires, vidéo, OAuth, Figma et suivi automatique sont hors périmètre de cette version.

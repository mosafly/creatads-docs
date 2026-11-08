# Studio agent : créer depuis le chat

Le **Studio** (`/studio`) réunit votre conversation, vos créations et l'aperçu du carrousel. Ce pilote personnel nécessite le compagnon local connecté à votre compte Codex et au même compte CreatAds. Son ouverture sur une instance dépend du déploiement des migrations, du backend et du frontend Studio.

1. Choisissez votre marque dans le studio. Ce choix détermine le Brand Kit et les créations accessibles.
2. Choisissez Instagram ou TikTok, puis Éditorial ou Produit.
3. Décrivez votre demande et utilisez la flèche d'envoi, ou Entrée. Maj + Entrée ajoute une ligne.
4. L'agent crée le carrousel et le sauvegarde dans **Mes créas**. Les étapes terminées apparaissent dans le chat.
5. Continuez dans la même conversation : « Raccourcis le titre de la slide 2 », « Passe le carrousel en style sombre » ou « Exporte-le en ZIP ».

Le rendu est visible à droite. Sélectionnez une slide pour préciser le contexte d'une demande. L'export apparaît dans le chat avec un bouton de téléchargement ; il contient les PNG ordonnés, la légende et le document éditable de la révision exportée. Aucune publication sur un réseau social n'est effectuée.

## Retrouver et reprendre

Les conversations et carrousels sont sauvegardés pour la marque sélectionnée. Rouvrez une conversation dans l'historique après avoir fermé la page. **Mes créas** permet aussi de rouvrir un projet dans le studio. Changer de marque change l'historique et les créations accessibles.

Une connexion interrompue n'annule pas automatiquement le travail en cours. Utilisez **Reprendre la demande** pour retrouver ses étapes enregistrées. Le bouton d'arrêt bloque la suite, mais conserve les modifications déjà sauvegardées. Si un projet a été modifié ailleurs, rouvrez sa version actuelle avant une nouvelle demande.

## Connexion et périmètre

L'inférence utilise le quota de votre abonnement Codex personnel. Il n'y a pas de repli payant vers une clé API ni de prix fixe annoncé par carrousel. Le compagnon doit rester actif pour générer, afficher les aperçus et exporter ; l'historique reste consultable via le service lorsqu'il est arrêté. Le compte CreatAds du navigateur doit correspondre à celui du compagnon.

Ce lot crée des carrousels graphiques de texte avec les couleurs et polices du Brand Kit. Les photos IA et les logos sous forme de calques ne sont pas encore inclus. Il s'agit d'un pilote personnel, pas d'une connexion ChatGPT commercialisée pour tous les utilisateurs du SaaS.

## Nouvelle interface et vidéo Higgsfield

Le studio propose des panneaux arrondis, une navigation compacte et un thème clair ou sombre. Sur mobile, les onglets **Conversation** et **Espace** affichent le chat ou le contenu. L’historique, Mes créas et Connexions sont accessibles depuis la barre de navigation.

Pour une vidéo, choisissez **Vidéo** ou demandez un storyboard dans le chat. Codex prépare de un à six plans avec le Brand Kit. Vous pouvez modifier le script depuis la conversation ; chaque version y reste enregistrée.

1. Relisez **Script & storyboard**.
2. Cliquez **Estimer la génération**. Le compagnon demande à Higgsfield le tarif des paramètres exacts de chaque plan.
3. Vérifiez le montant en dollars, puis cliquez **Confirmer et générer**. Le devis est valable cinq minutes. L’agent ne peut pas confirmer une dépense à votre place.
4. Gardez le studio ouvert pour suivre les clips et lancer les plans suivants du lot approuvé. Une réouverture reprend le suivi. **Arrêter les plans suivants** conserve les clips terminés ; un clip déjà en cours chez Higgsfield continue.
5. Quand tous les clips sont prêts, **Exporter le montage** produit un ZIP contenant un MP4 sans audio, un fichier SRT séparé, la légende et le storyboard. FFmpeg doit être installé sur le compagnon.

Le modèle de cette intégration est **Kling 4.0 Flash**, en 720p, à partir de texte, aux ratios 9:16, 16:9 ou 1:1. Le rendu exact des logos et des produits n’est pas garanti. La voix off, la musique, les avatars personnalisés et la publication sociale ne sont pas inclus dans ce premier raccord.

Le storyboard est sauvegardé dans la conversation cloud. Les clips, devis et états des générations restent dans le stockage privé du compagnon, séparés par client ; les vidéos apparaissent dans **Mes créas** du studio sur ce poste. Ce n’est pas encore une vidéothèque synchronisée entre plusieurs postes.

Higgsfield utilise un **solde API distinct** de Codex et de l’abonnement du site Higgsfield. Configurez `HF_API_KEY_ID` et `HF_API_KEY_SECRET` dans l’environnement du compagnon, ou dans son fichier privé `work/creatads-v2/.env.local`, puis redémarrez-le. Ne collez pas ces clés dans le chat. L’écran Connexions indique si elles sont configurées ; cela ne garantit pas qu’elles sont valides ni que le compte a du crédit.

Les appels réseau interrompus sont repris avec le même identifiant d’idempotence. Une génération échouée n’est pas remplacée automatiquement par une nouvelle dépense. Si le prix change avant le prochain plan, la suite est bloquée. Préparez une nouvelle version et relisez son devis. La génération vidéo en production nécessite la configuration et une validation réelle du compte API ; les tests avec fournisseur simulé ne la prouvent pas.

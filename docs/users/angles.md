# Angles créatifs

Les angles remplacent l’ancien modèle de profils/personas. Ils servent à varier le message et l’exécution visuelle plutôt qu’à sur-segmenter le ciblage Meta.

## Structure

| Champ | Rôle |
|---|---|
| Nom | Libellé interne court |
| Hook | Accroche publicitaire |
| Niveau de conscience | `unaware`, `problem_aware`, `solution_aware`, `product_aware`, `most_aware` |
| Style visuel | `ugc`, `studio`, `lifestyle`, `text_heavy`, `before_after`, `demo` |
| Direction de copy | Ton, bénéfice et CTA recommandés |

## Générer dix angles

1. Ouvrez `/brand-setup`.
2. Vérifiez le résumé de recherche.
3. Choisissez la langue.
4. Lancez la génération.

CreatAds génère dix angles répartis sur plusieurs niveaux de conscience et styles visuels. La génération d’angles ne consomme pas de crédit image.

## Gestion manuelle

Vous pouvez ajouter, modifier et supprimer un angle. Les angles appartiennent à l’espace client actif.

## Utilisation

- Mode Facile peut injecter un angle dans une génération.
- Mode Pro permet d’en sélectionner plusieurs ; chaque angle devient une dimension du lot.
- L’API, le SDK, le CLI et le MCP utilisent les actions/outils `list_angles`, `get_angle` et `generate_angles`.

L’ancien vocabulaire `profiles`, `list_profiles` et `generate_profiles` ne correspond plus au contrat actuel.

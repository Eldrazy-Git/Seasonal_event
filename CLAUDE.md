# Seasonal_event

## Ce projet fait partie de Toolkit

- Emplacement : `projets/seasonal-event/`, à l'intérieur du dossier Toolkit
  (la racine de Toolkit est donc `../..` depuis ce dossier).
- Ce projet peut être modifié librement depuis n'importe quel éditeur (VS Code, Android
  Studio, Notepad++…), sans passer par Toolkit.
- Sa fiche `projet.json` décrit le projet pour Toolkit (nom, type, point d'entrée,
  actions…). **Garde-la à jour** si tu changes le point d'entrée, ajoutes ou modifies une
  action (bouton), etc. Le format est documenté dans `../../app/docs/projet-json.md`.
- Explique ce que tu fais en français, simplement.
- Git : fais des **commits locaux** réguliers avec des messages clairs en français.
  **Ne fais jamais de push** : l'utilisateur pousse lui-même depuis Toolkit.
  Ne versionne jamais de secrets (clés, mots de passe, keystores, `.env`, `local.properties`).

## Rôle du projet

Ce dépôt pilote **entièrement** le décor saisonnier de l'app La Citadelle - Vote Compagnon,
sans mise à jour Play Store. Il ne contient pas de code : l'app télécharge ses fichiers depuis
GitHub. `seasonal_events.json` dit quand afficher chaque thème, `themes.json` décrit les thèmes
(particules, sprites, couleurs, effets) et `textures/` contient leurs images. Le format complet
est dans `README.md`.

## Technologies

Uniquement du JSON et des images. Aucune dépendance. Le code qui lit ces fichiers est dans l'app
Android : `../lacitadelle/app/src/main/java/fr/lacitadelle/votecompagnon/theme/` (`SeasonalChannel`,
`SeasonalEvents`, `SeasonalContentStore`, `SeasonalModels`).

## Lancer ou compiler

Rien à compiler ni à lancer. Pour vérifier le JSON avant de pousser :

```
python -m json.tool seasonal_events.json
python -m json.tool themes.json
```

L'aperçu se fait dans l'app de debug, avec le canal de test (voir `README.md`).

## Pièges

- **`main` est la production** : un push sur `main` atteint les apps publiées, à leur prochaine
  vérification quotidienne. Pour essayer un thème, utiliser la branche `test`, que seules les
  versions de debug peuvent lire.
- **Noms de fichiers et chemins** : l'app construit les URL à partir de `seasonal_events.json`,
  `themes.json` et `textures/<nom>` (`SeasonalChannel.baseUrl()`). Les renommer casse les apps
  déjà installées.
- **Clé mal écrite** : une clé de `seasonal_events.json` sans modèle du même nom dans `themes.json`
  ne provoque pas d'erreur, mais le décor reste vide pendant ces dates.
- **Chevauchements** : le premier événement de la liste gagne. Une longue période placée avant un
  événement plus court qu'elle contient empêche celui-ci de s'afficher.
- **Fenêtres intégrées** : un thème présent dans `seasonal_events.json` avec des dates valides
  touchant l'année en cours perd sa fenêtre intégrée pour cette année. Hors de ses dates, l'app
  affiche la saison (`SeasonalTheme.currentKey`). Le `README.md` ne le précise pas.
- **Limites côté app** : `themes.json` est refusé au-delà de 256 Ko (`MAX_JSON_BYTES`). Les autres
  limites sont dans `SeasonalContentStore.kt` et `SeasonalModels.kt`.
- **Moteur** : les `kind` de particules et les effets comme `spider` ou `fireworks` sont intégrés à
  l'app. En ajouter un demande une modification de `../lacitadelle/`. Un nouveau thème n'en demande
  pas s'il réutilise ce qui existe.

<!-- toolkit:projets-lies (section tenue à jour par Toolkit : ne pas la modifier à la main) -->
## Projets liés

« Seasonal_event » est lié à d'autres projets de l'utilisateur (même dossier `projets/`).
Avant une modification qui touche ce qu'ils partagent (format ou nom de fichiers, données,
API, ressources, versions…), lis le projet concerné et vérifie l'impact sur lui. Tu peux
lire leurs fichiers avec les chemins relatifs ci-dessous. Ne modifie un autre projet que si
l'utilisateur le demande ; sinon, dis-lui précisément ce qu'il faudra y changer, et dans
quel projet. Si tu découvres un lien qui n'est pas listé ici, signale-le-lui : les liens se
déclarent dans Toolkit (panneau du projet > « Projets liés »).

### Liens directs

- **La Citadelle - Vote Compagnon** (`../lacitadelle/`, application Android) — Application Android permettant de voter pour les différents sites de vote pour le serveur Minecraft Lacitadelle-mc.fr. Suivis du Rankup, des contrats, calculateur pour les objets Elytreum, Catalogue des items de Minecra…
  - Lien (déclaré par ce projet) : Permet de gérer le décor saisonnier de l'app

### Liens indirects (par l'intermédiaire d'autres projets)

- **Catalogue_tools** (`../catalogue-tools/`, script) — Utilitaire permettant la mise à jours des indexes, images et fichiers de recettes pour le Catalogue de l'application LaCitadelle. — via La Citadelle - Vote Compagnon
- **creative-tab-dumper** (`../creative-tab-dumper/`, projet Java (Gradle)) — Mod Minecraft permettant le lister les items de l'inventaire créatif et de donner leurs catégories. — via La Citadelle - Vote Compagnon → Catalogue_tools
<!-- /toolkit:projets-lies -->

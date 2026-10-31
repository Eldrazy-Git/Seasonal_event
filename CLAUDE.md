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

Ce dépôt pilote **entièrement** le décor saisonnier de l'app, sans mise à jour Play Store :

## Technologies

(À compléter.)

## Lancer ou compiler

(À compléter.)

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

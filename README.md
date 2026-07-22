# Configuration des événements saisonniers

`seasonal_events.json` pilote les fenêtres des thèmes d'arrière-plan de l'app
**sans mise à jour Play Store**.

## Emplacement

Le fichier est hébergé sur le dépôt GitHub public dédié :

- Édition : <https://github.com/Eldrazy-Git/Seasonal_event/blob/main/seasonal_events.json>
- URL lue par l'app :
  `https://raw.githubusercontent.com/Eldrazy-Git/Seasonal_event/main/seasonal_events.json`
  (constante `SeasonalEvents.CONFIG_URL` — la changer là si l'hébergement bouge)

L'app le télécharge **une fois par jour au maximum** : au lancement, elle
vérifie s'il a déjà été téléchargé depuis minuit (heure locale) ; sinon elle
le récupère et le met en cache. En cas d'échec (hors ligne…), le dernier
cache reste utilisé et un nouvel essai a lieu au prochain lancement.

Le fichier `seasonal_events.json` de ce dossier est un modèle de référence.

## Format

```json
{
  "events": [
    { "theme": "BIRTHDAY", "start": "2026-08-20", "end": "2026-08-31" }
  ]
}
```

- `theme` : `WINTER`, `SPRING`, `SUMMER`, `AUTUMN`, `HALLOWEEN`, `CHRISTMAS`,
  `NEW_YEAR`, `EASTER`
- `start` / `end` : dates locales **incluses**, format `AAAA-MM-JJ`
- En cas de chevauchement, le **premier** événement de la liste gagne
- Une entrée malformée est ignorée (les autres restent valides)

## Repli automatique

Si aucun événement ne couvre la date du jour — ou si le fichier est absent,
invalide ou injoignable — l'app retombe sur son calcul intégré :

| Thème | Fenêtre intégrée |
|---|---|
| Anniversaire | 24 Août – 30 Août |
| Halloween | 24 oct. – 1er nov. |
| Noël | 1er – 26 déc. |
| Nouvel An | 27 déc. – 3 janv. |
| Pâques | 7 jours avant le dimanche de Pâques (calculé) → lundi de Pâques |
| Saisons | équinoxes/solstices réels de l'année, calculés (ex. automne 2025 : 22/09, automne 2026 : 23/09) |

Pour désactiver tous les overrides, publier `{ "events": [] }`.

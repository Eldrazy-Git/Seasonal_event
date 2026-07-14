# Configuration des événements saisonniers

`seasonal_events.json` pilote les fenêtres des thèmes d'arrière-plan de l'app
**sans mise à jour Play Store**.

## Mise en place

Héberger le fichier à l'URL exacte :

```
https://lacitadelle-mc.fr/app/seasonal_events.json
```

(URL définie dans `SeasonalEvents.CONFIG_URL` — la changer là si besoin.)

L'app le télécharge au lancement, au plus une fois toutes les 6 h, et le met
en cache localement. Hors connexion, le dernier cache reste utilisé.

## Format

```json
{
  "events": [
    { "theme": "HALLOWEEN", "start": "2026-10-10", "end": "2026-11-03" }
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
| Halloween | 24 oct. – 1er nov. |
| Noël | 1er – 26 déc. |
| Nouvel An | 27 déc. – 3 janv. |
| Pâques | 7 jours avant le dimanche de Pâques (calculé) → lundi de Pâques |
| Saisons | météorologiques : mars-mai, juin-août, sept.-nov., déc.-févr. |

Pour désactiver tous les overrides, publier `{ "events": [] }`.

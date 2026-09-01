# Configuration des événements saisonniers

Ce dépôt pilote **entièrement** le décor saisonnier de l'app, sans mise à jour
Play Store :

| Fichier | Rôle |
|---|---|
| `seasonal_events.json` | **Quand** : fenêtres de dates des thèmes |
| `themes.json` | **Quoi** : modèles visuels complets (particules, sprites, couleurs, effets) |
| `textures/*.png` | Images optionnelles référencées par `themes.json` |

L'app ne contient **aucun modèle embarqué** : à l'activation du réglage
« Thème saisonnier », elle télécharge tout ce contenu dans son cache
(`filesDir/seasonal/`), puis vérifie **une fois par jour au maximum** s'il a
changé (requête conditionnelle ETag : si rien n'a bougé sur le dépôt, GitHub
répond `304` et **rien n'est re-téléchargé**). Les textures ne sont reprises
que si `themes.json` a changé. En cas d'échec (hors ligne…), le cache
existant reste utilisé et un nouvel essai a lieu au prochain lancement.

> Le CDN de GitHub (`raw.githubusercontent.com`) met ~5 minutes à refléter un
> push. Le bouton debug de l'app (« Télécharger ») le contourne avec une URL
> unique.

## `seasonal_events.json` — les dates

```json
{
  "events": [
    { "theme": "BIRTHDAY", "start": "2026-08-31", "end": "2026-09-06" }
  ]
}
```

- `theme` : **clé libre**, en MAJUSCULES. Soit une clé intégrée (`WINTER`,
  `SPRING`, `SUMMER`, `AUTUMN`, `HALLOWEEN`, `CHRISTMAS`, `NEW_YEAR`,
  `EASTER`, `BIRTHDAY`), soit une clé inédite (ex. `SAINT_VALENTIN`) — il
  suffit qu'un modèle du même nom existe dans `themes.json`.
- `start` / `end` : dates locales **incluses**, format `AAAA-MM-JJ`.
  Dates vides = événement désactivé (l'entrée reste dans le fichier).
- En cas de chevauchement, le **premier** événement de la liste gagne.
- Une entrée malformée est ignorée (les autres restent valides).

### Repli automatique des dates

Si aucun événement ne couvre la date du jour — ou si le fichier est absent,
invalide ou injoignable — l'app retombe sur son calcul intégré :

| Thème | Fenêtre intégrée |
|---|---|
| Halloween | 24 oct. – 1er nov. |
| Noël | 1er – 26 déc. |
| Nouvel An | 27 déc. – 3 janv. |
| Pâques | 7 jours avant le dimanche de Pâques (calculé) → lundi de Pâques |
| Saisons | équinoxes/solstices réels de l'année, calculés |

Pour désactiver tous les overrides de dates : `{ "events": [] }`.

## `themes.json` — les modèles visuels

Structure générale :

```json
{
  "version": 1,
  "sprites": { "nom_du_sprite": { … } },
  "themes":  { "CLÉ_DU_THÈME": { … } }
}
```

- `version` : numéro informatif, affiché dans le dialogue debug de l'app.
- L'**ordre** des thèmes dans le fichier = ordre du bouton de test debug.
- Un thème sans modèle valide → décor vide pour cette clé (jamais de crash) ;
  une entrée invalide (sprite, couleur, groupe de particules) est ignorée
  individuellement, le reste survit.

### Un thème

```json
"HALLOWEEN": {
  "label": "Halloween",          // libellé affiché (toasts, debug)
  "wind": 0,                     // vent horizontal en dp/s (−30 à 30, défaut 0)
  "spider": true,                // araignée suspendue (effet intégré au moteur)
  "fireworks": false,            // feux d'artifice (effet intégré au moteur)
  "fireworksDelay": 0.5,         // délai avant la 1re fusée (s)
  "fireworksColors": ["#F5D042"],// couleurs des gerbes (défaut : palette du moteur)
  "particles": [ … ],
  "wanderers": { … }
}
```

### `particles` — groupes de particules

```json
{ "kind": "SNOW", "count": 58, "colors": ["#FFFFFF", "#E8F4FF"] }
```

- `kind` : comportement physique, intégré au moteur —
  `SNOW` (chute + oscillation), `PETAL` / `LEAF` (chute pendulaire "falling
  leaves"), `CONFETTI` (chute + rotation), `SOUL` (montée pulsante),
  `FIREFLY` (vol de Lissajous clignotant), `BAT` (traversée horizontale,
  sprite intégré), `SPARKLE` (montée scintillante).
- `count` : nombre de base (max 150). Par défaut mis à l'échelle selon la
  hauteur d'écran ; `"scaled": false` pour un nombre fixe.
- `colors` : liste de `#RRGGBB` (ou `#AARRGGBB`), tirées au hasard.
- Optionnel — réglages fins, chacun `[min, max]` tiré par particule :
  `"speed"` (dp/s ; `FIREFLY` : Hz), `"sway"` (dp), `"freq"` (rad/s).
  Absents = défauts du moteur (recommandé).

### `wanderers` — sprites baladeurs

```json
"wanderers": {
  "mode": "DRIFT",               // défaut des items : DRIFT | FALL | SNOWFALL
  "alpha": 95,                   // opacité par défaut (0–255)
  "items": [
    { "sprite": "ghost" },
    { "sprite": "big_snowflake", "mode": "SNOWFALL", "spin": true },
    { "sprite": "bee", "flip": true },
    { "texture": "sunflower.png", "alpha": 120, "size": [24, 34] }
  ]
}
```

- Un item = **un** sprite à l'écran (répéter pour en avoir plusieurs).
- `sprite` : nom d'un sprite pixel-art déclaré dans `sprites`.
- `texture` : fichier de `textures/` (PNG/GIF/WebP, ≤ 512 Ko chacun, ≤ 4 Mo
  au total) ; `size` = largeur en dp `[min, max]` (défaut 24–34).
- `mode` : `DRIFT` (dérive libre, évite le logo), `FALL` (chute pendulaire),
  `SNOWFALL` (chute douce continue).
- `spin` : rotation lente sur soi-même. `flip` : miroir horizontal selon la
  direction du déplacement.
- Maximum 24 items par thème.

### `texts` — textes en police pixel-art

```json
"texts": [
  { "text": "Un an déjà ♥", "colors": ["#E05046", "#8CE060", "#F5D042", "#58C8D8"],
    "anchor": "logo", "offset": 10, "jitter": [70, 16], "angle": 8, "size": 3, "alpha": 210 }
]
```

- `text` : le texte affiché (max 64 caractères). Police pixel-art 5×7
  intégrée à l'app : A–Z, a–z, 0–9, accents français (é è ê ë à â ç ù û î ï
  ô ö ü É È À Ç), ponctuation courante (`! ? . , ' - + : ; ( ) « »`) et les
  symboles `♥` `★`. Un caractère inconnu est ignoré.
- `color` : couleur du texte (défaut `#E05046`, rouge).
- `colors` : liste de couleurs — l'une d'elles est **tirée au hasard à chaque
  lancement de l'app** (prioritaire sur `color`).
- `jitter: [x, y]` : décalage aléatoire max en dp, tiré à chaque lancement —
  horizontal (± x, ici ± 70) et vertical (vers le bas, 0 à y). La position
  est toujours ramenée dans l'écran (marge 8 dp).
- `angle` : inclinaison aléatoire max en degrés (± angle, tirée à chaque
  lancement ; 0–45, défaut 0).
- `anchor: "logo"` : **recommandé** — le texte se place centré **sous le
  logo Citadelle**, quel que soit l'appareil, et suit le défilement de
  l'écran. `offset` = écart sous le logo en dp (défaut 10). Sans ancre, le
  logo (dessiné par-dessus le décor) peut recouvrir le texte.
- `x` / `y` (ignorés si `anchor` est présent) : position du **centre** du
  texte, en fraction de l'écran, **entre 0 et 1, séparateur décimal = point**
  (`0.6`, jamais `0,6` — une virgule rend tout le fichier invalide et il est
  alors rejeté en bloc, l'ancien cache reste affiché). Défauts `0.5` / `0.3`.
- `size` : taille d'un pixel de la grille, en dp (1–8, défaut 2.8).
- `alpha` : opacité 0–255 (défaut 200).
- Maximum 4 textes par thème. Le texte oscille doucement de haut en bas et
  s'estompe devant les boutons de vote, glyphe par glyphe.

### `sprites` — pixel-art

```json
"ghost": {
  "flapRate": 2.5,               // frames/s (absent ou 0 = une seule frame)
  "palette": { "W": "#E8ECF2", "K": "#303848" },
  "frames": [
    [".WWWWW.", "WKWWKWW", "W.W.W.W"],
    [".WWWWW.", "WKWWKWW", ".W.W.W."]
  ]
}
```

- Chaque frame est une grille de caractères (max 32×32, **rectangulaire** :
  toutes les lignes d'une frame ont la même longueur, max 8 frames).
- Chaque caractère est peint avec la couleur de la `palette` ; un caractère
  absent de la palette (`.` par convention) = pixel transparent.

## Créer un événement inédit (ex. Saint-Valentin)

1. Ajouter le modèle dans `themes.json` :
   `"SAINT_VALENTIN": { "label": "Saint-Valentin", "particles": [ … ], … }`
   (+ sprites/textures si besoin).
2. Ajouter la fenêtre dans `seasonal_events.json` :
   `{ "theme": "SAINT_VALENTIN", "start": "2027-02-07", "end": "2027-02-15" }`.
3. Pousser. Les apps récupèrent le tout à leur prochaine vérification
   quotidienne — aucune mise à jour de l'app nécessaire.

## URLs lues par l'app

- `https://raw.githubusercontent.com/Eldrazy-Git/Seasonal_event/main/seasonal_events.json`
  (constante `SeasonalEvents.CONFIG_URL`)
- `https://raw.githubusercontent.com/Eldrazy-Git/Seasonal_event/main/themes.json`
  et `…/main/textures/<nom>` (constante `SeasonalContentStore.BASE_URL`)

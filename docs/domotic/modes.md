# Modes de la maison

DKNR Domotic publie lui-même une source nommée `dknr`, avec les modes généraux de la maison. Ils se pilotent comme n'importe quelle entité : depuis le tableau de bord, une fois leurs entités rangées dans une pièce (page **Entités**, source `dknr`), ou par MQTT. Ils servent aussi de déclencheurs aux scènes.


## Commandes et capteurs

Chaque mode a deux entités :

- une **commande**, `dknr/command/<mode>`, que vous choisissez : `auto`, `high`, `low` ou `manual` ;
- un **capteur**, `dknr/sensor/<mode>`, que DKNR Domotic calcule chaque minute et à chaque changement de la commande.

| Commande | Valeur du capteur |
| --- | --- |
| `auto` | Calculée : selon le jour et la nuit, la saison, l'occupation... |
| `high` | Forcée à l'état haut. |
| `low` | Forcée à l'état bas. |
| `manual` | `manual` : les scènes de cet élément ne pilotent plus rien, vous gardez la main. |

Les modes disponibles : `nycthemer` (jour / nuit), `light` (lumières), `shutter` (volets), `waterboiler` (chauffe-eau), `sound` (son).

D'autres entités `dknr` donnent l'état général de la maison : l'automatisme général (`automate`), le mode thermique (chauffage, refroidissement, hors gel, arrêt), l'humidité, le lever et le coucher du soleil, la puissance de la maison...


## Exemples MQTT

```bash
# Lumières en automatique
mosquitto_pub -t 'dknr/set/dknr/command/light' -m auto

# Volets en manuel pendant des travaux : les scènes ne les touchent plus
mosquitto_pub -t 'dknr/set/dknr/command/shutter' -m manual

# Lire le jour / la nuit calculé
mosquitto_sub -t 'dknr/get/dknr/sensor/nycthemer'
```


## Avec les scènes

- Une scène dont la cible est une lumière s'arrête quand `dknr/sensor/light` vaut `manual` (routine *modes*).
- Quand une commande change, les scènes déclenchées par le capteur correspondant sont rejouées quelques secondes plus tard : passer les volets de `manual` à `auto` les remet dans la position attendue.
- Ajoutez `dknr/sensor/nycthemer` comme déclencheur d'une scène pour qu'elle suive le jour et la nuit.

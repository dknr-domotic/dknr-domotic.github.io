# Le format DKNR

Toutes les sources publient leurs entités de la même façon sur le serveur MQTT. Ce format commun permet à DKNR Domotic, à dknr-alexa ou à n'importe quel autre outil de lire et piloter n'importe quel appareil sans connaître la technologie qui est derrière.


## Source, appareil, entité

Chaque valeur est identifiée par trois niveaux :

| Niveau | Exemple | Ce que c'est |
| --- | --- | --- |
| **source** | `zigbee` | Le projet qui publie : `zigbee`, `zwave`, `rfxcom`, `teleinfo`, `nuki`, `daikin`, `zendure`, `apsystems`, et `dknr` pour DKNR Domotic lui-même. |
| **appareil** (parent) | `00158d0001a2b3c4` | L'appareil dans la source, avec un identifiant stable : adresse IEEE Zigbee, numéro de nœud Z-Wave, identifiant radio... Renommer l'appareil ne le change pas. |
| **entité** | `temperature` | Une valeur de l'appareil : une température, un état, une position, une consigne... |

L'identifiant complet d'une entité est `source/appareil/entité`, par exemple `zigbee/00158d0001a2b3c4/temperature`.


## Les topics

Avec la racine `dknr` (réglable dans chaque projet, à garder identique partout) :

| Topic | Sens | Contenu |
| --- | --- | --- |
| `dknr/discovery/<source>` | source → | Description de la source : titre, version, dépôt, icône. Publiée aussi au démarrage de la source. |
| `dknr/discovery/<source>/<appareil>` | source → | Nom de l'appareil. |
| `dknr/discovery/<source>/<appareil>/<entité>` | source → | Description de l'entité (voir ci-dessous). |
| `dknr/get/<source>/<appareil>/<entité>` | source → | La valeur, publiée à chaque changement. |
| `dknr/set/<source>/<appareil>/<entité>` | → source | Une commande : la valeur voulue. |
| `dknr/room/<source>/<appareil>/<entité>` | DKNR Domotic → | L'identifiant de la pièce de l'entité. |
| `dknr/data/<type>/<id>/<champ>` | DKNR Domotic → | Des données qui ne sont pas des entités, comme les pièces. |

Description d'une entité (discovery) :

```json
{
  "name": "Température",
  "group": "sensor",
  "type": "number",
  "unit": "°C",
  "value": 21.5,
  "options": {}
}
```

- `type` vaut `binary`, `number`, `enum` (liste de choix) ou `text`.
- `options` donne, pour une liste, la correspondance entre la clé publiée et son libellé (traduit selon la langue choisie dans la configuration de la source).


## Les pièces

Seul DKNR Domotic range les entités par pièce. Il publie ce rangement pour que les autres projets puissent s'en servir, par exemple pour nommer ou regrouper les appareils :

| Topic | Valeur |
| --- | --- |
| `dknr/room/<source>/<appareil>/<entité>` | Identifiant de la pièce de l'entité. |
| `dknr/data/room/<id>/name` | Nom de la pièce. |
| `dknr/data/room/<id>/outdoor` | `1` si la pièce est extérieure, sinon `0`. |

- Ces topics sont **retenus** : un projet qui démarre reçoit tout de suite les pièces.
- Un message **vide** efface le topic : l'entité n'a plus de pièce, ou la pièce a été supprimée.
- Ils sont en **lecture seule** : on ne change une pièce que dans DKNR Domotic.
- Les pièces `home` et `system` existent toujours dans DKNR Domotic.

Les branches sous la racine (`discovery`, `get`, `set`, `room`) suivent toutes le chemin `<source>/<appareil>/<entité>`. La branche `data` contient le reste, sur le modèle `dknr/data/<type>/<id>/<champ>`, une valeur simple par topic.

Lire la pièce de toutes les entités Zigbee, puis le nom des pièces :

```bash
mosquitto_sub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/room/zigbee/#' -v
mosquitto_sub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/data/room/+/name' -v
```


## Les conventions

Ces règles sont communes à tous les projets DKNR. Elles évitent d'avoir à connaître les habitudes de chaque technologie (`ON` / `OFF`, `true` / `false`, `open` / `closed`...).

- **Binaires** : toujours `1` ou `0`. Pour piloter, `1`, `0` ou `TOGGLE` (inverser) ; la plupart des sources acceptent aussi `ON` et `OFF`.
- **Binaire inversé** (unité `rb`) : un binaire dont `0` est l'état actif. L'affichage en tient compte.
- **Ouvertures** (porte, fenêtre, baie) : `1` = ouvert, sauf unité `rb`.
- **Listes** : la valeur publiée est la **clé** de l'option, jamais le libellé. Une liste à deux états de type repos / détection (ex : `idle` / `occupied`) est publiée comme un binaire.
- **Actions de télécommande** : une liste d'actions, publiée à chaque appui, même s'il est identique au précédent. Elle n'est jamais rejouée depuis une valeur retenue.
- **Changements seulement** : une source ne republie une valeur que si elle change (sauf les actions de télécommande).
- **Couleur** : en hexadécimal `#rrggbb` (unité `chex`).


## Exemples

Lire toutes les valeurs Zigbee :

```bash
mosquitto_sub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/get/zigbee/#' -v
```

Allumer une prise Zigbee, puis l'inverser :

```bash
mosquitto_pub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/set/zigbee/00158d0001a2b3c4/state' -m 1
mosquitto_pub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/set/zigbee/00158d0001a2b3c4/state' -m TOGGLE
```

Passer les lumières de la maison en mode automatique (source interne `dknr`) :

```bash
mosquitto_pub -h 192.168.0.11 -u dknr -P mot-de-passe -t 'dknr/set/dknr/command/light' -m auto
```


## Home Assistant

La plupart des sources publient aussi la [discovery MQTT de Home Assistant](https://www.home-assistant.io/integrations/mqtt/#mqtt-discovery) sur `homeassistant/<type>/<source>/<appareil>_<entité>/config`. Les appareils apparaissent alors dans Home Assistant, lisibles et pilotables, en plus de DKNR Domotic.

Laissez vide le champ **Home Assistant Discovery topic** dans la page **Configurer** d'une source pour ne pas publier cette discovery. Si le logiciel sous-jacent publie déjà la sienne (Zigbee2MQTT, Z-Wave JS UI, rfxcom2mqtt), n'en gardez qu'une, sinon chaque appareil apparaît deux fois.

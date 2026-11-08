# Zigbee

**dknr-zigbee** reprend tous les appareils de [Zigbee2MQTT](https://www.zigbee2mqtt.io/) dans l'arborescence DKNR. Zigbee2MQTT gère la clé et le réseau Zigbee, dknr-zigbee traduit ses appareils en entités DKNR et les pilote.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-zigbee){ .md-button }


## Fonctionnalités

- **Tous les appareils supportés par Zigbee2MQTT** : capteurs, prises, interrupteurs, ampoules, volets, vannes thermostatiques, télécommandes...
- **Entités construites automatiquement** à partir de la description (*exposes*) de chaque appareil.
- **Pilotage** de toutes les valeurs modifiables : allumer, éteindre, inverser, luminosité, couleur, consigne, mode...
- **Page Réseau** : état du pont, liste des appareils (signal, batterie, dernier signal, disponibilité), appairage, renommage et suppression.
- **Discovery Home Assistant**, y compris le pont lui-même.


## Matériel

- Une clé ou un coordinateur Zigbee [compatible Zigbee2MQTT](https://www.zigbee2mqtt.io/guide/adapters/).
- Le serveur MQTT, partagé par Zigbee2MQTT et dknr-zigbee.


## Installation

Le `docker-compose.yaml` du dépôt lance **Zigbee2MQTT** et **dknr-zigbee**. Adaptez si besoin le port série de la clé (`/dev/ttyUSB0`) et configurez le serveur MQTT dans le `configuration.yaml` de Zigbee2MQTT (volume `data`).

Réglages de Zigbee2MQTT recommandés :

```yaml
availability:
  enabled: true      # ajoute l'entité Disponibilité
advanced:
  last_seen: ISO_8601  # ajoute l'entité Dernier signal
```

Dans la page **Configurer** de dknr-zigbee, renseignez aussi le **Zigbee2MQTT topic** (`zigbee2mqtt` par défaut).

!!! warning "Une seule discovery Home Assistant"
    Si l'intégration Home Assistant de Zigbee2MQTT est aussi activée, chaque appareil apparaît deux fois dans Home Assistant. Désactivez l'une des deux.


## Appairer un appareil

Page **Réseau** > **Autoriser** (254 secondes au maximum), puis mettez l'appareil en mode appairage. Il apparaît dans la liste dès que Zigbee2MQTT l'a interrogé.


## MQTT

L'identifiant d'un appareil est son adresse IEEE sans `0x` (ex : `00158d0001a2b3c4`), le nom d'une entité est la propriété Zigbee2MQTT (ex : `temperature`, `state`, `state_l1`).

| Topic | Contenu |
| --- | --- |
| `dknr/get/zigbee/<id>/<entité>` | Valeur, retenue (sauf `action`). Les listes à deux états (ex : `idle` / `occupied`) sont publiées en `1` / `0`. |
| `dknr/set/zigbee/<id>/<entité>` | `1`, `0` ou `TOGGLE` pour un binaire, un nombre ou une option pour le reste. |

- La couleur des ampoules est publiée et pilotée en `#rrggbb` (unité `chex`) sur l'entité `color`, la luminosité reste sur `brightness`.
- Le pont est l'appareil `bridge` : `state` (en ligne), `permit_join` (appairage, pilotable), `version`.
- Au démarrage, l'état des appareils sur secteur est relu une fois. Les appareils sur pile ne répondent qu'à leur réveil.

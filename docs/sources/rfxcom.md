# RFXCom

**dknr-rfxcom** traduit les appareils radio 433 MHz reçus par un **RFXCom RFXtrx433** : sondes météo, compteurs d'énergie, détecteurs, télécommandes, prises. Le RFXtrx est géré par [rfxcom2mqtt](https://github.com/rfxcom2mqtt/backend), qui publie les trames reçues sur MQTT.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-rfxcom){ .md-button }


## Fonctionnalités

- **Sondes météo** : température, humidité, confort, pression, prévision, pluie, vent, UV.
- **Énergie** : puissance, énergie, intensité par phase, tension, compteurs, téléinfo Cartelectronic.
- **Sécurité** : état, mouvement, alarme, sabotage.
- **Volets, thermostats, télécommandes** : dernière commande reçue et dernier signal.
- **Prises et interrupteurs** (lighting1, lighting2, lighting5) : état et pilotage.
- **Batterie et signal** en pourcentage.
- **Liste de blocage** pour ignorer les appareils des voisins.


## Matériel

- Un RFXCom **RFXtrx433** ou **RFXtrx433XL**.
- **rfxcom2mqtt** (image `rfxcom2mqtt/rfxcom2mqtt`), connecté au RFXtrx et au serveur MQTT.


## Installation

Le README du dépôt donne un `docker-compose.yaml` complet avec rfxcom2mqtt, configuré uniquement par variables d'environnement (`MQTT_SERVER`, `MQTT_USERNAME`, `MQTT_PASSWORD`, `RFXCOM_USB_DEVICE`).

!!! warning "rfxcom2mqtt"
    - rfxcom2mqtt attend `MQTT_USERNAME`, pas `MQTT_USER`.
    - Par défaut, il ne reçoit que quelques protocoles (`temperaturehumidity1`, `homeconfort`, `lighting1` à `lighting4`, `remote`, `security1`). Pour le vent, la pluie, l'énergie ou les volets, complétez `rfxcom.receive` dans un `config.yml` du volume `/app/data`.
    - Sa propre discovery Home Assistant est activée par défaut : n'en gardez qu'une.

Dans la page **Configurer** de dknr-rfxcom, renseignez le **RFXCom2MQTT topic** (`rfxcom2mqtt` par défaut) et cochez **Activation**. Les appareils apparaissent dans la page **Voir** dès leur première trame.


## Bloquer les voisins

Un RFXtrx reçoit aussi les sondes du voisinage. Page **Bloquer**, ajoutez une clé et une valeur du message à ignorer :

| Clé | Valeur | Ignore |
| --- | --- | --- |
| `id` | `0x5C02` | un appareil précis |
| `subTypeValue` | `RM174RF` | tout un modèle |
| `type` | `security1` | tout un protocole |


## Piloter une prise

Une prise 433 MHz ne répond pas par radio : dknr-rfxcom doit avoir **reçu au moins une fois sa télécommande** pour connaître son protocole et son code. Appuyez une fois sur la télécommande, l'entité `command<unité>` apparaît et devient pilotable, y compris après un redémarrage.

L'état affiché après une commande est l'état demandé : la prise ne confirme pas.


## MQTT

L'identifiant d'un appareil est son identifiant RFXCom sans `0x` (`5C02`).

| Topic | Contenu |
| --- | --- |
| `dknr/get/rfxcom/<appareil>/<entité>` | Valeur, à chaque changement. |
| `dknr/set/rfxcom/<appareil>/command<unité>` | `1` ou `0` pour une prise ou un interrupteur. |

Principales entités : `temperature`, `humidity`, `barometer`, `rainfall`, `windSpeed`, `windGust`, `uv`, `power`, `energy`, `current`, `voltage`, `motion`, `alarm`, `tampered`, `command<unité>`, `level<unité>`, `lastSeen`, `batteryLevel`, `rssi`. Les sondes à plusieurs mesures les numérotent (`temperature1`, `temperature2`...).

!!! info "Valeurs non retenues"
    Les valeurs RFXCom ne sont pas conservées sur le serveur MQTT : après un redémarrage, une sonde n'est connue qu'à sa prochaine émission.

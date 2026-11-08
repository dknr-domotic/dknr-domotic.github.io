# Zendure

**dknr-zendure** suit les onduleurs et batteries **Zendure** (testé avec un Hyper 2000) par l'API développeur MQTT de Zendure.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-zendure){ .md-button }


## Fonctionnalités

- **Onduleurs et batteries** : niveau de charge, puissances, températures, état.
- **Compteur Zendure 3CT** via un broker MQTT.
- **Énergie chargée et déchargée** calculée et publiée (`batteryChargeEnergy`, `batteryDischargeEnergy`, en kWh), utilisable dans le tableau de bord Énergie de Home Assistant.
- **Historique** de charge et de décharge dans InfluxDB : par heure, jour, mois et année.
- **Discovery Home Assistant**.

Dans DKNR Domotic, ces entités alimentent la batterie de la page **Énergie**.


## Matériel

- Un onduleur Zendure et ses batteries, associés à un compte Zendure.
- La machine Docker avec un accès à internet : l'API Zendure passe par son serveur.


## Installation

Le `docker-compose.yaml` du dépôt lance **dknr-zendure** et une base **InfluxDB 2** (`dknr_zendure_influxdb`). Ajoutez ensuite l'onduleur depuis la page **Ajouter**, avec l'adresse mail du compte Zendure et le numéro de série de l'onduleur.

Les variables `DKNR_INFLUX_*` permettent d'utiliser une autre base, comme pour [Teleinfo](teleinfo.md#installation) (bucket `zendure`).


## Énergie calculée

Zendure ne fournit que des puissances instantanées. dknr-zendure intègre toutes les 10 secondes la puissance de charge (`outputPackPower`) et de décharge (`packInputPower`) dans deux compteurs cumulés :

| Entité | Nom | Home Assistant |
| --- | --- | --- |
| `batteryChargeEnergy` | Énergie chargée | `device_class: energy`, `state_class: total_increasing` |
| `batteryDischargeEnergy` | Énergie déchargée | `device_class: energy`, `state_class: total_increasing` |

Les compteurs sont conservés au redémarrage. L'énergie n'est pas comptée pendant un arrêt de Node-RED, ni quand l'onduleur n'envoie plus rien pendant 15 minutes.

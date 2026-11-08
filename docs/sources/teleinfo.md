# Teleinfo

**dknr-teleinfo** lit la **téléinformation client** (TIC) d'un compteur électrique comme le Linky, décode ses trames et publie les index, la puissance et l'intensité sur MQTT. Il garde aussi son propre historique de consommation.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-teleinfo){ .md-button }


## Fonctionnalités

- **Lecture et décodage des trames TIC** : index par période tarifaire, puissance, intensité.
- **Historique de consommation** dans InfluxDB : par heure (24 dernières heures), par jour (7 derniers jours), par mois (12 derniers mois) et par an, détaillé par période tarifaire.
- **Discovery Home Assistant**.

Dans DKNR Domotic, ces entités alimentent la page **Énergie** (puissance du réseau, compteurs, tarif en cours) et la routine *tarif* des scènes.


## Matériel

- Un compteur avec sortie TIC (Linky, ou compteur électronique plus ancien).
- Une interface entre le compteur et la machine, par exemple une **clé USB TIC**.
- Les droits d'accès au port USB correspondant dans le conteneur.


## Installation

Le `docker-compose.yaml` du dépôt lance **dknr-teleinfo** et une base **InfluxDB 2** (`dknr_teleinfo_influxdb`) qui conserve une mesure par minute et par compteur, sans limite de durée. Donnez au conteneur l'accès au port de la clé TIC (`/dev/ttyUSB0` par exemple), puis ajoutez le compteur depuis la page **Ajouter un compteur** : un nom, un emplacement et le connecteur (port série) utilisé.

Aucune variable supplémentaire n'est obligatoire. Pour utiliser une autre base InfluxDB 2 :

| Variable | Par défaut |
| --- | --- |
| `DKNR_INFLUX_TOKEN` | `dknr-teleinfo-influxdb-token` |
| `DKNR_INFLUX_URL` | `http://dknr_teleinfo_influxdb:8086` |
| `DKNR_INFLUX_ORG` | `dknr` |
| `DKNR_INFLUX_BUCKET` | `teleinfo` |

!!! warning "InfluxDB"
    - Au premier démarrage, InfluxDB est créé avec `DKNR_USERNAME` et `DKNR_PASSWORD`, qui doit faire **8 caractères minimum**.
    - Le jeton par défaut convient tant que le port 8086 n'est pas exposé. Sinon, définissez le vôtre (`openssl rand -hex 32`).
    - Jeton, organisation et bucket ne sont appliqués qu'à la première initialisation.


## MQTT

Les valeurs sont publiées sur `dknr/get/teleinfo/<compteur>/<entité>`, avec leur discovery sur `dknr/discovery/teleinfo/...`. La source est en lecture seule.

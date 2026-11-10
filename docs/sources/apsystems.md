# APSystems

**dknr-apsystems** récupère la production des onduleurs solaires **APSystems** en local, sans le cloud APSystems, par l'API de l'ECU.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-apsystems){ .md-button }

![Tableau de bord dknr-apsystems](https://raw.githubusercontent.com/dknr-domotic/dknr-apsystems/master/sshot/dknr-apsystems_screenshot.png)


## Fonctionnalités

- **Plusieurs ECU**.
- **Lecture** de la production et des valeurs des onduleurs.
- **Historique de production** dans InfluxDB : par heure (24 dernières heures), jour (7 derniers jours), mois (12 derniers mois) et année.
- **Discovery Home Assistant**.

Dans DKNR Domotic, ces entités alimentent la production solaire de la page **Énergie** et le refroidissement sur énergie solaire du thermostat des scènes.


## Matériel

- Une ou plusieurs unités **ECU** APSystems (testé avec un **ECU-C**).
- La machine Docker sur le même réseau que les ECU.


## Installation

Le `docker-compose.yaml` du dépôt lance **dknr-apsystems** et une base **InfluxDB 2** (`dknr_apsystems_influxdb`). Ajoutez ensuite vos ECU depuis la page **Ajouter**.

Les variables `DKNR_INFLUX_*` permettent d'utiliser une autre base, comme pour [Teleinfo](teleinfo.md#installation) (bucket `apsystems`).

La production est calculée à partir des compteurs de l'ECU, relevés toutes les 5 minutes : `energy_today` pour les heures, `energy_total` pour les jours, mois et années. L'historique commence à la mise en place de la base.

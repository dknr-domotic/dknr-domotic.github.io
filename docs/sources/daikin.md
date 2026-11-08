# Daikin

**dknr-daikin** pilote les pompes à chaleur Daikin en local, sans le cloud Daikin, par l'API du module Wifi / Ethernet des unités intérieures.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-daikin){ .md-button }

![Tableau de bord dknr-daikin](https://raw.githubusercontent.com/dknr-domotic/dknr-daikin/master/sshot/dknr-daikin_screenshot.png)


## Fonctionnalités

- **Plusieurs unités intérieures**.
- **Lecture** des valeurs des unités (températures, mode, consigne, consommation...), avec graphiques dans le tableau de bord.
- **Pilotage** : marche / arrêt (`pow`), mode (`mode`), consigne (`stemp`), vitesse (`f_rate`) et direction (`f_dir`) de la ventilation.
- **Discovery Home Assistant** en lecture et pilotage.


## Matériel

- Une ou plusieurs unités intérieures Daikin avec module Wifi / Ethernet.
- La machine Docker sur le même réseau que les unités.

Testé avec le module **BRP069Bxx**. Devraient fonctionner : BRP069Axx, BRP069B4x, BRP072Axx.


## Installation

Le `docker-compose.yaml` du dépôt avec les variables communes. Ajoutez ensuite vos unités depuis la page **Ajouter** du tableau de bord (adresse IP de l'unité).


## Avec DKNR Domotic

L'interrupteur marche / arrêt d'une unité peut être piloté par une scène (refroidissement sur énergie solaire, par exemple) et partagé avec Alexa par [dknr-alexa](../integrations/alexa.md).

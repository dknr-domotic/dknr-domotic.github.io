# Sources

Une **source** est un projet DKNR qui parle à un type d'appareil et publie ses entités sur MQTT au [format DKNR](../demarrer/format-dknr.md). Chaque source est un conteneur Docker indépendant, avec son propre tableau de bord Node-RED.

| Source | Appareils | Passe par | Pilotage | Home Assistant |
| --- | --- | --- | --- | --- |
| [Zigbee](zigbee.md) | Capteurs, prises, ampoules, volets, vannes, télécommandes | Zigbee2MQTT | oui | oui |
| [Z-Wave](zwave.md) | Prises, variateurs, capteurs, serrures, télécommandes | Z-Wave JS UI | oui | oui |
| [RFXCom](rfxcom.md) | Sondes météo, énergie, sécurité, prises 433 MHz | rfxcom2mqtt | prises et interrupteurs | oui |
| [Teleinfo](teleinfo.md) | Compteur Linky (TIC) | clé USB TIC | non | oui |
| [Nuki](nuki.md) | Smart Lock, Smart Door, Opener | API HTTP du bridge, MQTT natif | oui | oui |
| [Daikin](daikin.md) | Pompes à chaleur | API locale du module Wifi | oui | oui |
| [Zendure](zendure.md) | Onduleurs et batteries | API MQTT Zendure | non | oui |
| [APSystems](apsystems.md) | Onduleurs solaires | API locale de l'ECU | non | oui |


## Ce qui est commun à toutes les sources

### Installation

Un `docker-compose.yaml` à la racine du dépôt, avec les variables `DKNR_USERNAME`, `DKNR_PASSWORD` et `DKNR_SECRET` (voir [Installation](../demarrer/installation.md)).

### La page Configurer

Dans le tableau de bord de chaque source :

- **Adresse IP**, **port**, **utilisateur** et **mot de passe** du serveur MQTT ;
- **Topic** : la racine DKNR, `dknr` par défaut ;
- **Home Assistant Discovery topic** : `homeassistant` pour publier la discovery Home Assistant, vide pour ne pas la publier ;
- la **langue** des libellés publiés (options des listes).

### Les identifiants

L'identifiant d'un appareil est toujours stable : il ne dépend pas du nom que vous lui donnez. Renommer un appareil dans Zigbee2MQTT ou Z-Wave JS UI ne change ni ses topics, ni ses entités dans DKNR Domotic ou Home Assistant.

### Le démarrage

Au démarrage, une source publie sa discovery (`dknr/discovery/<source>`). DKNR Domotic note l'heure et ignore pendant une minute les valeurs répétées de cette source pour ne pas déclencher de scènes par erreur.


## Écrire une nouvelle source

Tout outil capable de publier sur MQTT peut devenir une source : il suffit de respecter le [format DKNR](../demarrer/format-dknr.md) (discovery, `get`, `set`, binaires en `1` / `0`, clés pour les listes). Les projets existants, tous construits sur la même structure Node-RED, sont un bon point de départ : voir [Contribuer](../contribuer.md).

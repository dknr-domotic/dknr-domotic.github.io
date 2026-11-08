# Bien démarrer

Une installation DKNR se monte en quatre étapes : un serveur MQTT, une ou plusieurs sources, le tableau de bord DKNR Domotic, puis la configuration des pièces et des entités.


## Ce qu'il vous faut

- **Une machine qui fait tourner Docker** : un mini-PC, un NAS ou un Raspberry Pi 4 / 5. Les images existent en amd64 et arm64.
- **Un serveur MQTT** (Mosquitto par exemple), partagé par tous les projets. Si vous n'en avez pas, voir [Serveur MQTT](serveur-mqtt.md).
- **Le matériel de vos sources** : une clé Zigbee, un contrôleur Z-Wave, un RFXtrx433, une clé TIC pour le Linky... Chaque page de la section [Sources](../sources/index.md) indique ce qui est nécessaire.

!!! tip "Gestionnaire de conteneurs"
    Tous les projets se lancent avec un fichier `docker-compose.yaml`. Vous pouvez les déployer en ligne de commande ou depuis une interface comme Portainer, en collant le fichier dans une *stack* et en renseignant les variables d'environnement.


## Les étapes

1. **Le serveur MQTT** : installez-le, ou notez l'adresse, l'utilisateur et le mot de passe du vôtre. → [Serveur MQTT](serveur-mqtt.md)
2. **Les sources** : installez les projets correspondant à vos appareils. Dans la page **Configurer** de chacun, renseignez le serveur MQTT et gardez le topic `dknr`. Vos appareils apparaissent dans son tableau de bord. → [Sources](../sources/index.md)
3. **DKNR Domotic** : installez-le avec sa base InfluxDB, puis renseignez le même serveur MQTT dans sa page **Configuration**. Les entités de toutes les sources arrivent automatiquement. → [Installation](installation.md)
4. **La configuration** : créez vos pièces, donnez une pièce et un élément aux entités utiles, réglez l'énergie, puis écrivez vos premières scènes. → [Configuration](../domotic/configuration.md) et [Scènes](../domotic/scenes.md)

!!! info "Une nouvelle entité est cachée"
    Dans DKNR Domotic, une entité découverte reste cachée tant que vous ne lui avez pas donné une pièce et un élément dans la page **Entités**. C'est voulu : une clé Zigbee ou Z-Wave publie des centaines de valeurs techniques que vous ne voulez pas voir dans les pièces.


## Et ensuite

- Comprendre ce qui circule sur MQTT : [Le format DKNR](format-dknr.md).
- Piloter la maison à la voix : [dknr-alexa](../integrations/alexa.md).
- Ouvrir le tableau de bord à la famille, avec des droits adaptés : [Utilisateurs](../domotic/utilisateurs.md).

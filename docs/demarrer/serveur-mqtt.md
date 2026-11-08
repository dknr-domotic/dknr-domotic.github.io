# Serveur MQTT

Tous les projets DKNR échangent par un même serveur MQTT (un *broker*). Si vous en avez déjà un, par exemple celui de Home Assistant, utilisez-le : notez son adresse IP, son port (`1883` en général), un utilisateur et son mot de passe.


## Installer Mosquitto

Sinon, [Eclipse Mosquitto](https://mosquitto.org/) est un serveur léger qui s'installe en quelques lignes.

`docker-compose.yaml` :

```yaml
services:
  mosquitto:
    container_name: mosquitto
    image: eclipse-mosquitto:2
    restart: unless-stopped
    ports:
      - 1883:1883
    volumes:
      - ./mosquitto/config:/mosquitto/config
      - mosquitto_data:/mosquitto/data
volumes:
  mosquitto_data:
```

`mosquitto/config/mosquitto.conf` :

```text
listener 1883
allow_anonymous false
password_file /mosquitto/config/passwd
persistence true
persistence_location /mosquitto/data/
```

Créez ensuite l'utilisateur, puis redémarrez :

```bash
touch mosquitto/config/passwd
docker compose up -d
docker exec -it mosquitto mosquitto_passwd -b /mosquitto/config/passwd dknr un-mot-de-passe
docker compose restart mosquitto
```

!!! warning "Utilisez l'adresse IP réelle"
    Dans la configuration des projets, indiquez l'adresse IP de la machine qui héberge Mosquitto (ex : `192.168.0.11`), pas `localhost` : chaque projet tourne dans son propre conteneur.


## Persistance

Les sources publient la plupart de leurs valeurs en **retenues** (*retained*) : le serveur garde la dernière valeur de chaque entité et la renvoie à chaque nouvel abonné. Après un redémarrage, DKNR Domotic retrouve ainsi l'état de la maison sans attendre que chaque appareil se manifeste. Gardez la persistance activée.


## Observer ce qui circule

Un client comme [MQTT Explorer](https://mqtt-explorer.com/) affiche l'arborescence `dknr/...` en direct : c'est le moyen le plus simple de vérifier qu'une source publie bien. En ligne de commande :

```bash
docker exec -it mosquitto mosquitto_sub -u dknr -P un-mot-de-passe -t 'dknr/get/#' -v
```

Le détail des topics est expliqué dans [Le format DKNR](format-dknr.md).

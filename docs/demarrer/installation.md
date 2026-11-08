# Installation

Tous les projets DKNR s'installent de la même façon : une image Docker, un fichier `docker-compose.yaml` à la racine de chaque dépôt et trois variables d'environnement.


## Les variables communes

| Variable | Rôle |
| --- | --- |
| `DKNR_USERNAME` | Identifiant de l'éditeur Node-RED (et d'InfluxDB pour les projets qui en ont une). |
| `DKNR_PASSWORD` | Mot de passe de l'éditeur Node-RED (et d'InfluxDB, **8 caractères minimum** dans ce cas). |
| `DKNR_SECRET` | Mot secret qui chiffre les identifiants enregistrés par Node-RED (mot de passe MQTT, jetons...). Le changer oblige à les ressaisir. Il sert aussi de jeton InfluxDB pour DKNR Domotic. |

DKNR Domotic accepte en plus `DKNR_ADMIN_PASSWORD`, le mot de passe du compte `admin` de son tableau de bord (voir [Utilisateurs](../domotic/utilisateurs.md#le-compte-admin)).

!!! warning "Gardez le même `DKNR_SECRET`"
    Les identifiants sont chiffrés avec `DKNR_SECRET`. Si vous recréez un conteneur avec une autre valeur, Node-RED ne pourra plus les lire : il faudra ressaisir la configuration MQTT.


## Installer DKNR Domotic

Le `docker-compose.yaml` du dépôt [dknr-domotic](https://github.com/dknr-domotic/dknr-domotic) lance **InfluxDB 2** (historique des valeurs) et **DKNR Domotic** :

```bash
git clone https://github.com/dknr-domotic/dknr-domotic
cd dknr-domotic
cat > .env <<EOF
DKNR_USERNAME=admin
DKNR_PASSWORD=un-mot-de-passe-solide
DKNR_SECRET=une-longue-phrase-secrete
EOF
docker compose up -d
```

Une fois le conteneur démarré (environ une minute la première fois) :

- le tableau de bord est sur `http://<ip du serveur>:1880/dashboard` ;
- l'éditeur Node-RED sur `http://<ip du serveur>:1880/admin`.

Connectez-vous au tableau de bord avec le compte `admin` et votre mot de passe, puis passez à la [configuration](../domotic/configuration.md).


## Installer une source

Le principe est le même pour chaque source : récupérez le `docker-compose.yaml` de son dépôt, renseignez les trois variables et adaptez ce qui est propre au matériel (port série de la clé, par exemple). Certaines sources lancent aussi le logiciel qui gère le matériel (Zigbee2MQTT, Z-Wave JS UI) ou une base InfluxDB pour leur propre historique. La page de chaque [source](../sources/index.md) détaille ces points.

!!! tip "Plusieurs projets sur la même machine"
    Chaque projet expose son interface sur le port `1880` dans son `docker-compose.yaml`. Si vous en installez plusieurs sur la même machine, changez le port publié de chacun (`1881:1880`, `1882:1880`...).


## Les images

| Étiquette | Contenu |
| --- | --- |
| `latest` | Dernière version publiée. |
| `x.y` (ex : `1.4`) | Une version précise, qui ne change plus. |
| `dev` | Construite chaque matin depuis la branche principale, pour tester les nouveautés. |

Les images sont publiées sur Docker Hub (`jcpd/<projet>`) et sur GitHub (`ghcr.io/dknr-domotic/<projet>`).

Au démarrage, le conteneur récupère les flows Node-RED du dépôt : vos réglages (entités, pièces, scènes, utilisateurs...) sont enregistrés à part, dans le volume `/data`, et sont conservés.


## Mettre à jour

1. Récupérez la nouvelle image (`docker compose pull`, ou « Re-pull image » dans Portainer).
2. Recréez le conteneur (`docker compose up -d`).

Les volumes ne sont pas touchés. Pensez à faire un export de la configuration depuis la page **Configuration** de DKNR Domotic avant une mise à jour importante.

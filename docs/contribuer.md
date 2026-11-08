# Contribuer

Tous les projets DKNR sont **open-source**. Vous pouvez signaler un problème ou proposer une amélioration sur le dépôt concerné :

- un **ticket** (*issue*) pour un bug, une question ou une idée ;
- une **requête** (*pull request*) pour proposer une modification.

[Les dépôts de l'organisation](https://github.com/dknr-domotic){ .md-button }


## La structure d'un projet

Tous les dépôts suivent la même organisation :

| Fichier | Rôle |
| --- | --- |
| `flows.json` | Les flows Node-RED (en mode projet). |
| `flows_cred.json` | Les identifiants, chiffrés avec `DKNR_SECRET`. Aucun secret en clair dans le dépôt. |
| `package.json` | La version du projet. |
| `data/settings.js` | Réglages de Node-RED : éditeur sur `/admin`, tableau de bord FlowFuse Dashboard 2. |
| `Dockerfile`, `docker-entrypoint.sh` | L'image, basée sur `nodered/node-red`. Au démarrage, le conteneur récupère les flows du dépôt. |
| `docker-compose.yaml` | Le lancement du projet, avec ses éventuels compagnons (Zigbee2MQTT, InfluxDB...). |
| `.github/workflows/` | Publication des images : une version (`release.yaml`, manuel), l'image `dev` chaque matin. |

Certains projets ont aussi un `docs/CONCEPTION.md`, qui décrit l'architecture et les conventions internes : à lire avant de modifier les flows.


## Modifier un projet

1. Lancez un conteneur de développement du projet (sans vos vrais appareils si possible).
2. Dans l'éditeur Node-RED (`/admin`), modifiez les flows. Le gestionnaire de projets de Node-RED permet de committer et de pousser vers votre fork.
3. Respectez le [format DKNR](demarrer/format-dknr.md) : binaires en `1` / `0`, clés pour les listes, identifiants stables.
4. Proposez une *pull request* en décrivant le changement et comment vous l'avez testé.


## Ce site

Ce site est construit avec [Zensical](https://zensical.org/) à partir de fichiers Markdown, dans le dépôt [dknr-domotic.github.io](https://github.com/dknr-domotic/dknr-domotic.github.io). Le crayon en haut de chaque page ouvre sa source pour proposer une correction.

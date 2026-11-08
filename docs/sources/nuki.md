# Nuki

**dknr-nuki** pilote les serrures Nuki en local, sans le cloud Nuki : il utilise l'API HTTP des bridges et, en option, l'API MQTT native des Smart Lock récentes.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-nuki){ .md-button }


## Fonctionnalités

- **Plusieurs bridges** (matériel ou logiciel) et tous leurs appareils.
- **Smart Lock 1.0 à 4.0, Smart Door, Opener**.
- **Appairage simplifié** : sans jeton, le projet le demande au bridge, il suffit d'appuyer sur son bouton.
- **Jeton chiffré** pour ne pas faire circuler le jeton en clair.
- **Mises à jour instantanées** par le callback du bridge, enregistré automatiquement.
- **Smart Lock en MQTT natif** (3.0 Pro, 4.0) reprises dans l'arborescence DKNR.
- **Pilotage** : verrouiller, déverrouiller, ouvrir, Lock'n'Go, verrouillage complet, Ring to Open, mode continu.
- **Discovery Home Assistant** : serrure, sélecteur d'action, batterie, capteur de porte, sonnette.


## Matériel

- Un ou plusieurs appareils Nuki.
- Un bridge avec l'**API HTTP activée** (application Nuki > Bridge > Gérer le bridge > API HTTP), ou une Smart Lock avec MQTT natif sur le même serveur MQTT.
- La machine Docker sur le même réseau que les bridges.


## Ajouter un bridge

Page **Ajouter** :

- **Adresse IP** et **port** du bridge (8080 par défaut) ;
- **Jeton API** : laissez vide et appuyez sur le bouton du bridge dans les 30 secondes, ou recopiez le jeton de l'application ;
- **Jeton chiffré** : recommandé avec un bridge matériel (l'horloge du serveur doit être à l'heure), à décocher pour un bridge logiciel ;
- **URL de callback** (facultatif) : l'adresse où le bridge joint le projet, par exemple `http://192.168.0.20:1880/nuki/callback` (3 callbacks au maximum par bridge).

L'état est relu toutes les 30 secondes, et immédiatement avec le callback.


## MQTT

L'identifiant d'un appareil est son Nuki ID en hexadécimal, tel qu'imprimé sur l'appareil.

| Topic | Contenu |
| --- | --- |
| `dknr/get/nuki/<id>/<entité>` | Valeur, retenue. |
| `dknr/set/nuki/<id>/lock` | `LOCK`, `UNLOCK` ou `OPEN`. |
| `dknr/set/nuki/<id>/action` | Nom de l'action (ex : `Verrouiller`, `Lock'n'Go`) ou son numéro Nuki. |

Entités Smart Lock : `lock`, `action`, `state`, `mode`, `battery`, `batteryCritical`, `batteryCharging`, `keypadBatteryCritical`, `doorsensorState`, `door`, `lastAction`, `timestamp`, `firmware`. L'Opener a en plus `ring` et `ringTimestamp`. Seules les entités fournies par l'appareil sont publiées.

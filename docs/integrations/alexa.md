# Alexa

**dknr-alexa** partage les entités DKNR vers et depuis Amazon Alexa. Les appareils Alexa sont choisis et nommés depuis son tableau de bord, sans configurer un nœud Node-RED par appareil. Le lien avec Alexa passe par [Virtual Smart Home](https://github.com/csuermann/node-red-contrib-virtual-smart-home) (VSH) : **aucune connexion entrante depuis internet** n'est nécessaire.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-alexa){ .md-button }

!!! warning "Virtual Smart Home est un service par abonnement"
    dknr-alexa s'appuie sur le service Virtual Smart Home, qui relie Node-RED à Alexa par son propre serveur. Ce service est **payant, par abonnement** : la formule PRO, nécessaire ici, permet jusqu'à 200 appareils et la lecture de leur état par Alexa. Sans abonnement actif, la connexion à Alexa ne fonctionne pas. Voir les conditions sur le [site de VSH](https://github.com/csuermann/node-red-contrib-virtual-smart-home).


## Fonctionnalités

- **Toutes les sources DKNR** : les entités sont lues depuis la discovery DKNR, sans dépendre de DKNR Domotic.
- **Types d'appareils** : interrupteur (lumière, prise, VMC, climatisation...), ouverture (porte, fenêtre, baie), mouvement, température, volet.
- **Appareils groupés** : un appareil Alexa peut piloter plusieurs entités (ex : « guirlande » = deux prises).
- **Pilotage vocal** : « Alexa, allume la lumière de l'atelier », « Alexa, ouvre le volet de la véranda », et routines Alexa sur les ouvertures, mouvements et températures.
- **Connexion à Amazon depuis le tableau de bord**, sans passer par l'éditeur Node-RED.


## Ce qu'il faut

- Le serveur MQTT et au moins une source DKNR.
- Un compte Amazon avec des appareils Echo.
- Un **abonnement payant** Virtual Smart Home, formule PRO (200 appareils).
- Un accès sortant à internet pour le conteneur.


## Mise en place

1. **Installer** avec le `docker-compose.yaml` du dépôt et les variables communes. `DKNR_SECRET` chiffre aussi les certificats de la connexion Amazon : le changer oblige à refaire la connexion.
2. **MQTT** : page **Configurer**, le même serveur et le même topic que les autres projets. La page **Connexion** liste ensuite les sources reçues.
3. **Amazon** : page **Connexion** > **Connecter à Amazon**. Un code s'affiche : ouvrez `amazon.com/code` sur un téléphone connecté au compte des Echo et saisissez-le. Activez une fois la skill **Virtual Smart Home** dans l'application Alexa.
4. **Appareils** : page **Appareils** > **Nouvel appareil** : un type, une source, une ou plusieurs entités compatibles, le **nom Alexa** (celui dit à la voix) et la pièce.
5. **Appliquer** : bouton **Appliquer les modifications** de la page **Connexion** pour envoyer à Alexa les nouveaux appareils, les changements de nom ou de type et les suppressions. Puis « Alexa, recherche des appareils » si besoin.

!!! warning "Une connexion, une installation"
    Une même connexion VSH ne doit pas tourner sur deux installations à la fois. Arrêtez l'ancien flow VSH avant de connecter dknr-alexa.

!!! tip "Les pièces"
    VSH ne transmet pas les pièces à Alexa : rangez aussi les appareils dans les mêmes pièces de l'application Alexa.


## Comment les valeurs sont traduites

**DKNR vers Alexa** : l'état d'un appareil vient de sa première entité.

| Type | Entité DKNR | État Alexa |
| --- | --- | --- |
| Interrupteur | binaire ou variateur | allumé si `1` ou supérieur à 0 |
| Ouverture | binaire | ouvert si `1` (`0` pour l'unité `rb`) |
| Mouvement | binaire | mouvement si `1` |
| Température | nombre | °C, au dixième |
| Volet | nombre (0-99 ou 0-100) ou binaire | pourcentage d'ouverture |

Pour respecter les conditions de VSH, une température n'est envoyée que si elle change d'au moins 0,5 °C ou toutes les 15 minutes au plus, les autres états seulement quand ils changent. Les dernières valeurs sont gardées : après un redémarrage, les états sont renvoyés à Alexa sans attendre.

**Alexa vers DKNR** : un ordre est publié sur `dknr/set/...` pour chaque entité de l'appareil.

- Interrupteur : `1` / `0`. Pour un variateur, « allume » envoie `99` (sauf s'il est déjà allumé) et « éteins » `0`.
- Volet : le pourcentage demandé, `99` au plus.

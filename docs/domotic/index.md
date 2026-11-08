# DKNR Domotic

**DKNR Domotic** est le tableau de bord et le cerveau de l'installation. Il réunit toutes les entités publiées par les sources, les range par pièce, garde leur historique dans InfluxDB et les pilote par des scènes, le tout en local.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-domotic){ .md-button }
[Installation](../demarrer/installation.md#installer-dknr-domotic){ .md-button }


## Les pages du tableau de bord

| Page | Ce qu'on y trouve |
| --- | --- |
| **Accueil** | Le plan de la maison avec la température et l'humidité de chaque pièce, la météo (OpenWeatherMap) et les prochains événements du calendrier. Un clic sur une pièce l'ouvre. |
| **Pièces** | Les entités de chaque pièce classées par usage (lumières, volets, ouvertures, températures...), le pilotage direct, l'historique de chaque valeur et les caméras. On y règle aussi les heures de confort et les jours travaillés de la pièce. |
| **Énergie** | La carte des flux (réseau, solaire, batterie, maison, lumières), les graphiques de puissance, d'énergie et de batterie, et le tarif en cours. |
| **Scènes** | Les automatismes : création, déclencheurs, minuteurs en cours, événements, et le détail du dernier passage de chaque scène pour comprendre ce qu'elle a fait. → [Scènes](scenes.md) |
| **Calendrier** | Anniversaires et événements, affichés sur l'accueil. |
| **Entités** | Pour chaque source : le nom, la pièce, l'élément, le groupe, l'unité et la visibilité de chaque entité. |
| **Configuration** | Serveur MQTT, pièces, utilisateurs, sauvegarde et restauration, remise à zéro du quotidien des scènes. |
| **Authentification** | Connexion, déconnexion, choix de la langue (français, anglais). |

Les pages visibles dépendent du niveau de l'utilisateur connecté : voir [Utilisateurs](utilisateurs.md).


## Pièces et éléments

Chaque entité affichée appartient à une **pièce** et a un **élément**, qui dit ce qu'elle représente et comment l'afficher et la piloter :

| Famille | Éléments |
| --- | --- |
| Commandes | interrupteur, prise, lumière, volet, curseur, serrure, chauffe-eau, ventilation, son, mode |
| Ouvertures | porte, fenêtre |
| Capteurs | température, humidité, luminosité, pression, mouvement, alerte, batterie, direction |
| Énergie | puissance, intensité, tension, fréquence, énergie, tarif |
| Divers | télécommande, heure, texte, emplacement, couleur |

L'élément sert aussi aux scènes : les routines proposées pour une cible dépendent de son élément (un thermostat n'a pas les mêmes réglages qu'une lumière).


## Historique

Chaque valeur reçue est enregistrée dans InfluxDB (bucket `domotic`, une mesure par élément). Le détail d'une entité dans la page **Pièces** affiche son historique en graphique. Les pages **Énergie** et **Accueil** s'appuient aussi sur cet historique.


## La source `dknr`

DKNR Domotic est lui-même une source, nommée `dknr`. Il publie les modes de la maison (automatisme général, jour / nuit, lumières, volets, chauffe-eau, son...) et des capteurs calculés sur les mêmes topics que les autres sources. → [Modes de la maison](modes.md)


## Sauvegarde

La page **Configuration** exporte la configuration dans un fichier JSON, en choisissant les données à inclure : comptes, MQTT, pièces, appareils, entités, entités bloquées, calendrier, énergie, scènes. L'import ne remplace que les données présentes dans le fichier et n'ajoute que les comptes absents.

Le lien de téléchargement de l'export est à usage unique et valable 5 minutes.

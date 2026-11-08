# Configuration

Une fois DKNR Domotic [installé](../demarrer/installation.md#installer-dknr-domotic), connectez-vous avec le compte `admin` et suivez ces étapes dans l'ordre.


## 1. Le serveur MQTT

Dans **Configuration > MQTT** :

- **Adresse IP**, **port**, **utilisateur** et **mot de passe** du serveur MQTT, le même que celui des sources.
- **Topic** : la racine des topics DKNR, `dknr` par défaut, identique dans tous les projets.

Dès la connexion, DKNR Domotic lit la discovery de toutes les sources : sources, appareils et entités sont créés automatiquement.


## 2. Les pièces

1. Dans **Configuration > Pièces**, créez vos pièces. Cochez **Extérieur** pour les pièces dont les sondes donnent la température extérieure (jardin, terrasse) : le thermostat des scènes s'en sert.
2. Dans la page **Pièces**, menu de la pièce :
    - **Plan** : envoyez une image du plan de la maison, puis placez chaque pièce dessus (**Emplacement**).
    - **Caméra** : l'adresse du flux d'une caméra, affiché dans la pièce.
    - **Jours travaillés** et **Heures de confort** : les plages où la pièce est occupée, utilisées par le chauffage et les scènes (routines *confort* et *éco*). Une plage peut passer minuit.


## 3. Les entités

Dans la page **Entités**, choisissez une source, puis pour chaque entité utile :

- un **nom** clair (« Lumière salon », « Température chambre ») ;
- une **pièce** ;
- un **élément** (lumière, volet, porte, température...) ;
- éventuellement le **groupe** et l'**unité**.

Une nouvelle entité reste **cachée** tant qu'elle n'est pas configurée. Les entités inutiles peuvent être bloquées pour ne plus être enregistrées.

!!! tip "Par où commencer"
    Une clé Zigbee ou Z-Wave publie beaucoup de valeurs techniques (qualité du signal, paramètres...). Commencez par les entités que vous voulez voir et piloter tous les jours : lumières, volets, températures, ouvertures. Le reste peut attendre.


## 4. L'énergie

Dans la page **Énergie**, menu **Entités**, choisissez les entités qui alimentent la carte des flux et les graphiques : puissance du réseau (Teleinfo), production solaire (APSystems), batterie (Zendure), compteurs d'énergie et tarif en cours. L'entité de tarif préférentiel sert aussi à la routine *tarif* des scènes.

La consommation des lumières est calculée à partir des appareils qui ont une lumière visible et une mesure de puissance.


## 5. La météo

L'accueil affiche les prévisions d'[OpenWeatherMap](https://openweathermap.org/api) (API gratuite, prévisions sur 5 jours), relues toutes les heures. Pour les activer :

- renseignez votre **clé d'API OpenWeatherMap** avec les entités de l'énergie (page **Énergie**, menu **Entités**) ;
- donnez la position de la maison avec les entités **latitude** et **longitude** de la source `dknr` (elles servent aussi au calcul du lever et du coucher du soleil).


## 6. Les utilisateurs

Créez un compte par personne dans **Configuration > Nouvel utilisateur**, avec un niveau adapté : voir [Utilisateurs](utilisateurs.md).


## Sauvegarder

Une fois la configuration faite, exportez-la depuis **Configuration > Export / import**. Gardez ce fichier à l'abri : il contient les comptes et la configuration MQTT.

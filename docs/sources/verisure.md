# Verisure

**dknr-verisure** lit et pilote une alarme **Verisure France**. Verisure ne propose aucun accès local à sa centrale : le projet passe par le cloud Verisure, avec la même API que l'application My Verisure.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-verisure){ .md-button }

!!! warning "Une source qui dépend du cloud"
    Sans internet ou en cas de panne chez Verisure, l'état de l'alarme n'est plus lu et les commandes ne passent plus. L'API utilisée n'est pas publique : Verisure peut la changer sans préavis. Ce projet n'est ni édité ni soutenu par Verisure.


## Fonctionnalités

- **Connexion avec le compte My Verisure**, validation par SMS comprise. Le **mot de passe n'est jamais enregistré** : seul le jeton de session est conservé et renouvelé automatiquement.
- **Plusieurs installations** d'un même compte.
- **État de l'alarme** : désarmée, partielle jour, partielle nuit, totale (et les combinaisons périmétrique / annexe en lecture).
- **Pilotage** de l'alarme intérieure : désarmer, partielle jour, partielle nuit, totale.
- **Désarmement par MQTT désactivé par défaut**.
- **Journal** des événements (mises en service, désactivations, alarmes, coupures de courant...), avec leur auteur.
- **Discovery Home Assistant** : sélecteur de mode, état, dernier événement.


## Matériel

- Une alarme Verisure France et un compte My Verisure. Créez de préférence un utilisateur réservé au projet dans l'application, et validez-le sur le site Verisure avant la première connexion.
- La machine Docker avec un accès à internet.


## Connecter le compte

Page **Compte** :

- **Identifiant** et **mot de passe** My Verisure. Attention, Verisure bloque le compte après plusieurs mots de passe erronés.
- Si Verisure demande une validation : choisissez le **numéro de téléphone**, cliquez sur **Recevoir le code par SMS**, puis saisissez le **code reçu**. Elle n'est demandée qu'une fois.

La ligne **Dernière erreur** donne la réponse exacte de Verisure en cas d'échec. Si Verisure ferme la session, le projet ne retente jamais seul une connexion : il faut se reconnecter depuis cette page.

Chaque installation porte un nom, « Alarme » par défaut, modifiable dans la page **Alarme**. L'alias donné par Verisure est l'adresse de l'installation : il n'est jamais publié sur MQTT.


## Lecture et pilotage

L'état est lu toutes les 180 secondes par défaut (60 au minimum, réglable dans **Configurer**). Verisure bloque temporairement les accès trop fréquents : le projet suspend alors ses lectures pendant 15 minutes. Un changement fait au clavier ou depuis l'application est donc connu avec ce retard.

Dans la page **Alarme**, les boutons **Désarmer**, **Jour**, **Nuit** et **Totale** changent le mode ; une commande prend une dizaine de secondes.

- Passer d'un mode armé à un autre **désarme puis réarme** l'alarme.
- Si une porte ou une fenêtre est ouverte, Verisure refuse l'armement et la zone est indiquée. L'armement forcé n'est pas géré.

Comme la source dépend du cloud, l'entité **`fault`** passe à `1` dès que le compte n'est plus connecté ou que l'alarme ne répond plus depuis un moment : servez-vous-en comme alerte.

!!! danger "Désarmement par MQTT"
    Par défaut, MQTT peut **armer mais pas désarmer** : tout appareil capable de publier sur votre serveur MQTT pourrait sinon couper l'alarme. Pour l'autoriser, cochez **Autoriser le désarmement par MQTT** dans la page **Configurer**. Sans cette option, un changement de mode d'une alarme déjà armée est aussi refusé par MQTT.

    Le tableau de bord du projet permet de désarmer : ne l'exposez pas en dehors de votre réseau local.


## Journal

La page **Journal** liste les 100 derniers événements connus de Verisure. Il est lu à chaque changement d'état, à l'ouverture de la page, et sinon toutes les 15 minutes.


## MQTT

L'identifiant d'un appareil est le numéro de l'installation Verisure.

| Topic | Contenu |
| --- | --- |
| `dknr/get/verisure/<id>/<entité>` | Valeur, retenue. |
| `dknr/set/verisure/<id>/mode` | `D`, `P`, `Q` ou `T` (ou le nom du mode). Une commande retenue est ignorée. |
| `dknr/set/verisure/<id>/refresh` | Lecture immédiate (une par 20 secondes au plus). |

| Entité | Valeur |
| --- | --- |
| `fault` | `1` en défaut : compte non connecté, ou aucune lecture valide depuis 3 intervalles de lecture (10 minutes au minimum). `0` sinon. |
| `mode` | Mode de l'alarme intérieure, en lecture et en commande : `D` désarmée, `P` partielle jour, `Q` partielle nuit, `T` totale. |
| `state` | État complet : les valeurs de `mode`, plus `E`, `B`, `C`, `A` (avec périmétrique) et `X`, `R`, `S`, `O` (avec annexe). |
| `armed` | `1` si l'alarme est armée, quel que soit le mode. |
| `wifi` | `1` si la centrale est connectée en wifi. |
| `event` | Texte du dernier événement, tel que donné par Verisure. |
| `event_type` | Type du dernier événement : `armed`, `disarmed`, `alarm`, `arming_failed`, `power_cut`, `power_restored`... (liste complète dans le dépôt). |
| `event_by` | Auteur du dernier événement : utilisateur, badge, détecteur... |
| `event_time` | Date du dernier événement (ISO 8601). |
| `timestamp` | Date de la dernière mise à jour connue de Verisure (ISO 8601). |

L'API Verisure ne donne pas l'état des détecteurs d'ouverture ni de mouvement.

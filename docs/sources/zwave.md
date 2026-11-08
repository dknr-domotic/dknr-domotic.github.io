# Z-Wave

**dknr-zwave** reprend les nœuds de [Z-Wave JS UI](https://github.com/zwave-js/zwave-js-ui) dans l'arborescence DKNR. Z-Wave JS UI gère le contrôleur et le réseau, dknr-zwave traduit les nœuds en entités DKNR et les pilote.

[Dépôt GitHub](https://github.com/dknr-domotic/dknr-zwave){ .md-button }


## Fonctionnalités

- **Tous les appareils supportés par Z-Wave JS** : prises, interrupteurs, variateurs, capteurs, serrures, télécommandes...
- **Entités construites automatiquement** à partir des valeurs de chaque nœud. La valeur courante et la valeur cible d'une commande forment une seule entité.
- **Pilotage** de toutes les valeurs modifiables, y compris les paramètres de configuration.
- **Page Réseau** : passerelle, contrôleur, liste des nœuds (batterie, activité, état), inclusion, exclusion, renommage, emplacement, suppression d'un nœud défaillant.
- **Discovery Home Assistant**. Les paramètres de configuration y sont créés désactivés, les entités d'un nœud mort deviennent indisponibles.


## Matériel

- Un contrôleur Z-Wave (clé USB) compatible Z-Wave JS.
- Le serveur MQTT, partagé par Z-Wave JS UI et dknr-zwave.


## Installation

Le `docker-compose.yaml` du dépôt lance **Z-Wave JS UI** et **dknr-zwave**. Adaptez si besoin le port série du contrôleur (`/dev/ttyACM0`).

Dans **Settings** de Z-Wave JS UI :

- **MQTT** : le serveur MQTT avec son adresse IP réelle. **Disable Gateway** reste décoché, **Retain** activé. Notez le **Prefix** (`zwave` par défaut).
- **Gateway** : activez **Send Z-Wave Events**. Les événements donnent les changements immédiatement, quel que soit le *Payload type* : vous pouvez garder vos réglages, par exemple *Just value*.
- **Home Assistant** : avec *Just value*, la MQTT Discovery de Z-Wave JS UI ne peut pas être activée. Laissez-la désactivée, dknr-zwave publie la sienne.

Dans la page **Configurer** de dknr-zwave, renseignez le **Z-Wave JS UI topic** (le Prefix). La passerelle est trouvée automatiquement.

!!! info "Sans les événements"
    Sans **Send Z-Wave Events**, les valeurs ne sont relues que toutes les 5 minutes. La page **Réseau** indique si les événements arrivent.


## Inclure un appareil

Page **Réseau** > **Inclure**, puis mettez l'appareil en mode inclusion. Pour un appareil sécurisé S2 (serrure, alarme...), faites l'inclusion depuis Z-Wave JS UI, qui demande le code DSK.


## MQTT

L'identifiant d'un appareil est son numéro de nœud (ex : `5`), le nom d'une entité est `<command class>_<endpoint>_<propriété>[_<clé>]` (ex : `37_0_currentValue`, `49_0_Air_temperature`).

| Topic | Contenu |
| --- | --- |
| `dknr/get/zwave/<nœud>/<entité>` | Valeur, retenue (sauf scènes et notifications). Les listes à deux états (repos / détection, ouvert / fermé) sont publiées en `1` / `0`. |
| `dknr/set/zwave/<nœud>/<entité>` | `1`, `0` ou `TOGGLE` pour un binaire, un nombre, un libellé ou sa valeur pour une liste. |

- Chaque nœud a aussi `status` (Vivant, Endormi, Réveillé, Mort) et `lastActive`.
- La passerelle est l'appareil `controller` : `state`, `inclusion` et `exclusion` (pilotables), `version`.
- Les command classes de gestion du protocole (association, version, sécurité...) ne sont pas publiées.

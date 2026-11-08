# Utilisateurs

Le tableau de bord peut être ouvert à toute la famille : chaque personne a son compte, sa langue et un niveau qui décide de ce qu'elle voit et de ce qu'elle peut faire.


## Les niveaux

Du plus bas au plus haut :

| Niveau | Peut |
| --- | --- |
| *non connecté* | Voir la page d'authentification, rien d'autre. |
| **visitor** | Voir les mêmes pages qu'un enfant (accueil, énergie, calendrier, pièces), sans aucune action : les commandes sont affichées mais désactivées. |
| **kid** | Piloter toutes les entités des pièces. |
| **parent** | En plus : les scènes, le calendrier, les heures de confort et les jours travaillés des pièces, la remise à zéro du quotidien, la liste des utilisateurs (pour les comptes kid et visitor). |
| **admin** | Tout : entités, configuration MQTT, pièces, plan, caméras, énergie, sauvegarde, création des comptes. |

Chaque action est vérifiée par le serveur, pas seulement masquée dans l'interface : un utilisateur ne peut pas contourner son niveau.

??? note "Détail par page"
    | Page | Action → niveau minimal |
    |---|---|
    | Accueil | voir, ouvrir une pièce → visitor |
    | Énergie | voir → visitor ; choisir les entités → admin |
    | Calendrier | voir → visitor ; ajouter, supprimer → parent |
    | Pièces | voir, détail et historique → visitor ; piloter → kid ; scène de la pièce, heures de confort, jours travaillés → parent ; renommer, caméra, emplacement, plan → admin |
    | Scènes | tout → parent |
    | Entités | tout → admin |
    | Configuration | voir, remise à zéro du quotidien, liste des utilisateurs → parent ; MQTT, divers, sauvegarde, nouvel utilisateur, pièces → admin |


## Le compte admin

Le compte `admin` existe toujours. Son mot de passe **ne se change pas dans l'interface** : il vient des variables d'environnement du conteneur et il est appliqué à chaque démarrage.

- `DKNR_ADMIN_PASSWORD` si elle est définie ;
- sinon `DKNR_PASSWORD`, le même que l'éditeur Node-RED.

Pour changer le mot de passe, modifiez la variable (dans votre `docker-compose.yaml` ou dans Portainer) et redémarrez le conteneur.

!!! warning "Connexion automatique"
    **Configuration > Divers** permet d'être connecté automatiquement en admin, sans mot de passe. Pratique sur une tablette murale dans un réseau de confiance, mais à désactiver dès que le tableau de bord est accessible à d'autres.


## Les autres comptes

- **Créer** : **Configuration > Nouvel utilisateur** (admin), avec un identifiant, un mot de passe, un niveau et une langue.
- **Déconnecter ou supprimer** : **Configuration > Utilisateurs**. Un admin agit sur tous les autres comptes, un parent seulement sur les comptes kid et visitor, personne sur son propre compte.


## Les sessions

Une session est conservée au rafraîchissement de la page et au redémarrage du conteneur. Elle expire après une durée d'inactivité réglable dans **Configuration > Divers** : 6 heures, 24 heures ou 30 jours. Les onglets d'un même navigateur partagent la session.

Lestiboudois Maxime
Berberat Alex
# GRS - Lab01 - Rapport

## Partie 1 - Configurer un serveur Syslog
 >1. Montrez, avec une copie d’écran, les événements reçus par votre serveur Syslog
 ![image_événements_reçus_serveur_syslog](2025-10-02_15-48-37.png)

## Partie 2 - Configurer un client Linux
>2. Montrez votre fichier de configuration (les commandes importantes)

La ligne importante à ajouter dans le fichier de configuration est la suivante:
```
*.* @ 192.168.26.11:514
```
Elle permet de rediriger les log de la machine linux vers la machine Windows 10, qui possède l'adresse ip statique 192.168.26.11, sur le port 514

>3. Montrez les messages reçus sur la console du serveur Syslog distant (Windows
10).

Log envoyé depuis la machine Linux:
![log depuis la machine linux](2025-10-03_17-26-29.png)

Log reçu sur Windows 10:
![log reçu sur Windows](2025-10-03_17-26-55.png)

>4. Donnez plusieurs exemples de messages qui vous semblent utiles dans la gestion
des réseaux.

Messages utiles pour la gestion:
- Connexion SSH échouée ou réussie
- Tentatives sudo 
- Messages kernel (erreur réseau, problème de disque, etc)
- Log de service critique
- Alertes de firewall

????????????

>5. Que pouvez-vous dire sur la sécurité des échanges de messages Syslog ?

La sécurité des échanges de messages Syslog par UDP est très faible, pas du tout sécurisé même. Voici plusieurs vulnérabilité à prendre en compte:
- falsification -> spoofing d'adresses IP
- interception -> écoute réseau
- pertes de paquets -> udp non fiable

>6. Présentez et expliquer la captures wireshark d’un message Syslog.

![log reçu sur Windows](2025-10-03_17-26-55.png)

Prenons notre message envoyé précédemment `Hello GRS`:

On peut donc remarquer qu'un message wireshark contient plusieurs champs nous indiquand plusieurs types d'information: \
**Time**: indique la date et l'heure à laquelle le message a été émis. \
**IP**: indique l'adresse Ip source du message. \
**Host**: indique le nom de l'entité qui a envoyé. le message \
**Facility**: Les différentes catégories de messages et permettent de classer les logs selon leur source.

Liste des Facilities standards Syslog:
|Facility||
|:------:|:--|
|kern|messages su noyau Linux|
|user|messages générés par les utilisateurs/applications|
|mail|système de messagerie|
|daemon|processus en arrère-plan(services système)|
|auth/authpriv|authentification et autorisation (login, sudo, ssh, etc)|
|syslog|messages générés par le daemon syslog lui-même|
|lpr|système d'impression|
|news|serveurs de news (NNTP, usenet)|
|crpm|tâche planifiées|
|ftp|serveur FTP|
|local0 à local7|réservé à un usage local ou personnalisé|

**Priority**: indique la priorité du message
|Priority|Code||
|:------:|:--:|:--|
|emerg|0|Situation d'urgence: le système est inutilisable, ex: Kernel Panic|
|alert|1|Action immédiate requise, ex: disque plein, service critique HS, etc|
|crit|2|Erreur critique du système ou d'un service|
|err|3|Erreur standard, ex échec d'une commande, problème applicatif|
|warning|4|Avertissement, anomalie non bloquante|
|notice|5|Information importante mais pas une erreur|
|info|6|Message informatif général|
|debug|7|Message de debug très détaillé, utilisé pour le diagnostic|

**Tag**: ne représente pas une catégorie officielle, mais servent à identifier quelle application ou processus a généré le log. \
**Message**: donne le contenu du log

En prenant le message que nous avons envoyé précédemment depuis le noeud Linux et en le lisant depuis wireshark sur Windows, nous pouvons dire:
- Il a été envoyé le 3 ocotobre à 15h26min27sec.
- L'adresse ip l'ayant envoyé est la 192.168.26.12 (soit notre noeud linux).
- Le host l'ayant envoyé est `grs-srv, soit encore notre noeud linux.
- La facility est `user`, c'est donc un message généré par l'utilisateur ou une application (première option ici).
- La priority est notice, soit d'ordre 5: l'information est importante mais n'est pas une erreur.
- Le tag est `grs`.
- Le message contient `Hello GRS`.

>7. Modifiez votre configuration afin que les messages Syslog générés par la commande
`sudo` (et exclusivement ceux-ci) soient stockés dans le fichier local
`/var/log/sudos.log`

Il faut ajouter dans le fichier `/etc/rsyslog.conf` les lignes suivantes :
```
if ($programname == 'sudo') then {
    action(type="omfile" file="/var/log/sudos.log")
    stop
}
```

avant la ligne de redirection ajoutée précédemment:
```
*.* @192.168.26.11:514
```
## Partie 3 - Configurer Syslog sur un équipement réseau

>8. Montrer les commandes IOS que vous avez utilisé.

>9. Montrer les commandes IOS que vous avez utilisé.

>10. Montrer les commandes IOS que vous avez utilisé.

>11. Montrer le message reçu

## Partie 4 - Rediriger les événements Windows sur un serveur Syslog

>12. Montrez la commande et le message reçu sur le serveur Syslog

>13. Montrez la commande utilisée et les messages reçus par le serveur Syslog

>14. Montrez le contenu du script et le message reçu par le serveur Syslog

>15. Montrez le bon fonctionnement de la redirection à l’aide d’une copie d’écran du
serveur Syslog

## Partie 5 - Utilisation de Sysmon

>16. Montrez le contenu de votre fichier XML.

>17. Montrez le message reçu.
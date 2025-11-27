
# GRS - Lab02
## Objectif 3 : Configurer les agents SNMP en mode v2
*Installer/configurer les agents SNMP en mode v2.*


- Activez l’agent SNMP sur la machine Windows 10. Le paramétrage s’effectue au niveau du service correspondant. Définissez le community string en mode RO avec la valeur choisie à l’objectif 2 (par exemple heig).
---
> 1. Montrez à l’aide de captures d’écran les changements de configuration que vous avez réalisés


#### **Réponse:**

![Changements SNMP config](image.png)

---

- A l’aide du browser SNMPb, interrogez le localhost.
---
> 2. Montrez les valeurs retournées par les 5 objets SysDescr, SysName, SysUpTime,
ifNumber, et l’adresse IP de votre cible. \
Vérifiez si les données retournées via SNMP correspondent à la réalité du système cible (Windows).

#### **Réponse:**

<u>SysDescr:</u>

![alt text](image-2.png)

![alt text](image-3.png)


<u>SysName:</u>

![alt text](image-4.png)

![alt text](image-5.png)

<u>SysUpTime</u>

![alt text](image-8.png)

![alt text](image-9.png)

<u>ifNumber</u>

![alt text](image-6.png)

![alt text](image-7.png)


<u>adresse IP</u>

![alt text](image-10.png)

![alt text](image-11.png)

---
- Activer et configurez l’agent SNMP sur le routeur Cisco 
---

> 3. Montrez la configuration du routeur cisco de manière à ce qu’il puisse être géré via
SNMPv2 (choisissez ciscoRO comme community string read only et ciscoRW
comme community string read write).
Configurez également le routeur pour qu’il envoie ses traps snmp au manager SNMPb sur Windows. Prévoyez la synchro temps et l’affichage des événements en ms.

#### **Réponse:**

```
enable
conf t 
snpm-server community ciscoRO ro
snmp-server community ciscoRW rw
snmp-server enable traps
snmp-server enable traps snmp authentication
```

![alt text](image-12.png)

Nous nous sommes rendu compte qu'il y avait une typo lors de la capture d'écran pour ciscoRO, nous l'avons donc refaite correctement ensuite.

---

> 4. Montrez les valeurs retournées par les 5 objets SysDescr, SysName, SysUpTime, sysObjectID, ainsi l’adresse IP de votre cible (obtenue via SNMP)

#### **Réponse:**

<u>SysDescr</u>

![alt text](image-13.png)

![alt text](image-14.png)

<u>SysName</u>

![alt text](image-15.png)

![alt text](image-16.png)

<u>SysUpTime</u>

![alt text](image-17.png)

![alt text](image-18.png)

<u>sysObjectID</u>

![alt text](image-19.png)

![alt text](image-20.png)

<u>adresse IP</u>

![alt text](image-21.png)

![alt text](image-22.png)

---

> 5. A quoi sert/correspond la valeur retournée par sysObjectID ? Que vous manque-t-il pour l’interpréter correctement ?

#### **Réponse:**

La valeur renvoie une partie de l'OID, identifiant le constructeur et le modèle de l'équipement. Les gestionnaires SNMP utilisent le sysObjectID pour identifier le type d’équipement ainsi que ses capacités. Cette valeur peut ensuite être associée aux informations d’un module MIB afin d’obtenir des détails précis sur l’appareil, comme sa version matérielle ou logicielle.

Il manque la racine complète (1.3.6.1.4.1) et le fichier MiB du constructeur (CISCO-PRODUCT-MIB).



---

> 6. A l’aide de Wireshark, capturez et présentez de manière lisible les trames lorsque la machine Windows 10 interroge le routeur Cisco pour obtenir le nom de l’équipement (les champs concernant SNMP doivent être visibles et commentés).


#### **Réponse:**

![alt text](image-24.png)

1. Version: version numéro 1 de SNMP
2. Community: Community string utilisé pour l'authentification, afin d'accéder à l'appareil.
3. data: get-next-request: Message utilisé pour avoir la valeur d'un OID.
   1. request-id: ID de demande défini, identificateur unique utilisé pour faire correspondre la demande avec la réponse correspondante.
   2. error-status: noError indique qu'aucune erreur ne s'est produite lors de la demande SNMP
   3. error-index: valeur à 0, indique qu'aucune erreur n'a été commise avec l'OID de la demande
4. variable-binding: Liste de paires OID-valeur, spécifiant les objets sur lesquels porte la demande (ici un seul objet)
   1. OID: 1.3.6.1.2.1.1.5 (iso.3.6.1.2.1.1.5) ->objet de description du système
   2. Value Null: indique que la valeur de l'objet est demandé mais pas définie.

---

- Changez le nom (hostname) du routeur à l’aide de l’application SNMPb (nouveau nom : `router-<votre-nom>`) tout en capturant avec Wireshark les messages échangés.

---
> 7. Montrez et analysez l’échange de messages capturés par Wireshark.

#### **Réponse:**
![alt text](image-42.png)
1. version: version numéro 1 du protocole SNMP
2. Community: ciscoRW: Community string utilisé pour l'authentification (RW)
3. Data: Payload du message SNMP
   1. Set-request: type du message SNMP: ici, on veut définir la valeur d'une variable
   2. Request-ID: identficateur unique de la requête SNMP.
   3. error-status: noError (0), signifie qu'il n'y a pas eu d'erreur lors du traitement de la demande. 
   4. error-index: index indiquant quelle variable a provoqué une erreur lors du traitement de la demande. 0 ici, indique qu'il n'y a pas eu d'erreur.
   5. variable-bindings: liste de paire OID-valeur. Ici, une seule paire.
      1. OID: 1.3.6.1.2.1.1.5.0 (iso 3.6.1.2.1.1.5.0) correspond à notre hostname
      2. Value (OctetString): Valeur que l'on souhaite attribuer. Ici "routeur-maxime" est le nouveau nom.

![alt text](image-43.png)
1. version: version numéro 1 du protocole SNMP
2. Community: ciscoRW: Community string utilisé pour l'authentification (RW)
3. Data: Payload du message SNMP
   1. Get-request: type du message SNMP: ici, on veut récupérer/donner la valeur d'une variable
   2. Request-ID: identificateur unique de la requête SNMP.
   3. error-status: noError (0), signifie qu'il n'y a pas eu d'erreur lors du traitement de la demande. 
   4. error-index: index indiquant quelle variable a provoqué une erreur lors du traitement de la demande. 0 ici, indique qu'il n'y a pas eu d'erreur.
   5. variable-bindings: liste de paire OID-valeur. Ici, une seule paire.
      1. OID: 1.3.6.1.2.1.1.5.0 (iso 3.6.1.2.1.1.5.0) correspond à notre hostname
      2. Value (OctetString): Valeur de la réponse
   
   La requête Set portait sur la modification du nom du système, et la réponse renvoie la valeur "routeur-maxime". Cela indique que l’agent SNMP du périphérique a bien pris en compte la demande et renvoie désormais le nom du système mis à jour.

--- 

- Générez une trap SNMP en déclenchant un événement sur votre routeur (un peu d’imagination...) tout en capturant avec Wireshark les messages échangés.

---
> 8. Montrez les messages (traps) reçus par l’application SNMPb.

#### **Réponse:**
![alt text](image-26.png)

On retrouve sur cette image:
- le numéro de la trap : 0003
- la date : 2025-11-26
- l'heure à laquelle la trap a été effectuée: 17:13:57
- le timestamp: temps interne de l'équipement
- le Notification type: Type de trap, ici linkUp
- le Message Type: ici une Trap(v2)
- la version du protocole SNMP: SNMPv2c
- l'agent address, soit l'IP de l'équipement qui envooie les traps, ici 192.168.26.13 (routeur cisco)
- l'agent port: porte source utilisé par l'agent, ici 50867.

Le trap linkUp indique que l’équipement SNMP (agissant comme agent) signale qu’une de ses interfaces réseau est passée de down à up (c’est-à-dire, qu’un lien réseau a été activé).

On observe 3 variables (bindings) accompagnant le trap :

ifIndex : 1, qui identifie l’interface réseau concernée. Elle porte l’index 1.

ifDescr : GigabitEthernet1, soit le nom de l’interface : GigabitEthernet1

ifType : 6, soit le type d’interface selon l’IF-MIB. 6 indique ethernetCsmacd, soit "Interface Ethernet classique."

La communauté SNMP utilisée est ciscoRO

---

> 9. Analysez les trames de la capture précédente et décodez la signification des différents messages SNMP en recherchant la signification du « OID code » à l’aide du SNMP Object Navigator Cisco.

*[https://snmp.cloudapps.cisco.com/Support/SNMP/do/BrowseOID.do](https://snmp.cloudapps.cisco.com/Support/SNMP/do/BrowseOID.do)* \
*(compte Cisco à créer si nécessaire)*

#### **Réponse:**
![alt text](image-44.png)
| Variable                  | OID                           | Valeur                                            | Signification                                                                            |
| ------------------------- | ----------------------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **sysUpTime**             | 1.3.6.1.2.1.1.3.0             | 715886                                            | Temps écoulé depuis le dernier redémarrage de l’équipement (en centièmes de seconde).    |
| **snmpTrapOID**           | 1.3.6.1.6.3.1.1.4.1.0         | 1.3.6.1.4.1.9.9.41.2.0.1                          | Identifie le trap spécifique ; ici, un événement Cisco de type LINK (trouble interface). |
| **ciscoNotificationType** | 1.3.6.1.4.1.9.9.41.2.3.1.2.89 | "LINK"                                            | Type d’événement : modification de l’état d’une interface.                               |
| **ciscoEventIndex**       | 1.3.6.1.4.1.9.9.41.2.3.1.3.89 | 4                                                 | Identifiant interne de l’événement pour l’équipement Cisco.                              |
| **ciscoEventSeverity**    | 1.3.6.1.4.1.9.9.41.2.3.1.4.89 | "UPDOWN"                                          | Gravité : changement d’état (up/down) d’une interface.                                   |
| **ciscoEventDescription** | 1.3.6.1.4.1.9.9.41.2.3.1.5.89 | "Interface GigabitEthernet1, changed state to up" | Description textuelle de l’événement — interface redevenue UP.                           |
| **ciscoEventTime**        | 1.3.6.1.4.1.9.9.41.2.3.1.6.89 | 715885                                            | Horodatage du moment exact de l’événement (proche de `sysUpTime`).                       |


---

> 10. Montrez la configuration de votre routeur afin qu’il n’accepte des requêtes SNMP que de la part de votre machine Windows uniquement.

#### **Réponse:**

Configuration:
```{bash}
conf t
access-list 10 permit 192.168.26.11
access-list 10 deny any
snmp-server community ciscoRO RO 10
snmp-server community ciscoRW RW 10
exit
copy run start
```

![alt text](image-28.png)


---
- Afin d’intégrer votre nœud linux à votre environnement de gestion, activez et configurez SNMP sur votre nœud Linux.

---
> 11. Montrez le(s) fichier(s) de configuration nécessaire à la configuration de SNMP sur votre nœud Linux (même community string que pour Windows.).

#### **Réponse:**

Fichier de configuration `/etc/snmp/snmpd.conf`:

![alt text](image-29.png)

---

> 12. Montrez le résultat dans SNMPb d’une requête permettant de connaître la durée de
fonctionnement de votre nœud Linux.

#### **Réponse:**

![alt text](image-30.png)

---

- Windows Powershell permet de créer des scripts, utiles pour récupérer des informations de manière régulière et automatisée par exemple.

---
> 13. Montrez la commande (par exemple via l’installation du module SNMP) utilisée depuis Windows pour récupérer le nom de votre routeur Cisco.

#### **Réponse:**

```{bash}
$SNMP = New-Object -ComObject olePrn.OleSNMP
Write-Output "Device name: $($SNMP.open('192.168.26.13','ciscoRO',2,1000) | Out-Null; $SNMP.get('.1.3.6.1.2.1.1.5.0')); $SNMP.Close()"
```

---

> 14. Montrez la commande ou le script utilisé pour récupérer toutes les minutes la liste des processus/programmes actifs sur votre machine Windows.

*Intégrez le fichier MIB standard HOST-RESSOURCES-MIB à SNMPb (fourni par SNMPb) pour déterminer les OID à utiliser.*



#### **Réponse:**

```PowerShell
while ($true) {
    Get-SNMPData -IPAddress localhost -Community heig -Walk -OID "1.3.6.1.2.1.25.4.2.1.2"
    Start-Sleep -Seconds 60
}
```

---




## Objectif 4 : MIBs privées

- Afin d’interroger des objets spécifiques à votre équipement, vous avez besoin d’intégrer à votre manager SNMP (l’application SNMPb) les MIB privées nécessaires.  
Vous désirez obtenir des informations sur la mémoire flash embarquée sur votre routeur : chargez les MIBs privées nécessaires

---
> 15. Donnez la liste des fichiers MIBs que vous avez chargé et expliquez comment vous
avez déterminé ce choix.


#### **Réponse:**

|Fichiers MIBs| Raisons|
|--|--|
|`CISCO-FLASH-MIB`|Cela nous permet d'avoir les informations de la mémoire flash|
|`CISCO-SMI`| C'est une dépendance de CISCO-FLASH-MIB|

---

> 16. Montrez, via une requête SNMPb, le nom des 10 premiers fichiers stockés sur la mémoire flash de votre routeur Cisco.

#### **Réponse:**

![alt text](image-31.png)
Les 10 premiers fichiers sont donc:
![alt text](image-32.png)

---


## Objectif 5 : Configurer les agents SNMP en mode v3

La version 3 de SNMP ajoute des capacités de chiffrement et d’authentification « forte ».

---
> 17. Montrez la configuration de votre router afin qu’il n’accepte plus que des requêtes SNMPv3 en mode authentifié et chiffré.


#### **Réponse:**

Il faut commencer par supprimé la config SNMP v2:
```sh
# Del SNMP v2 config:
no snmp-server community ciscoRO RO 10
no snmp-server community ciscoRW RW 10
```

Ensuite, configurer SNMP v3:
```sh
# Config SNMP v3
snmp-server group SECURE-GROUP v3 priv
snmp-server user secureuser SECURE-GROUP v3 auth sha pass1 priv aes 128 pass2
snmp-server host 192.168.26.11 version 3 priv secureuser
```

(pas de screen du fichier de configuration, car les données liées sont trop éloignées les unes des autres)

---

> 18. Montrez la configuration en mode SNMPv3 de votre application SNMPb et montrer le résultat d’une requête sur la valeur SysUpTime (MIB-2) en SNMPv3.

#### **Réponse:**

![alt text](image-33.png)

![alt text](image-34.png)

![alt text](image-35.png)


---

> 19. Capturez/analysez les messages lors d’une requête SNMP v3.



#### **Réponse:**

![alt text](image-36.png)


Analyse:
- `msgVersion: snmpv3 (3)` -> Indique la version de SNMP utilisé.
- `msgGlobalData`
  - `msgID: 1507332` -> Identifiant unique du message SNMP.
  - `msgMaxSize: 4096` -> Taille maximal des messages SNMP.
  - `msgFlags: 07` -> Indique les flags de traitement des messages, ici 07 indique qu'une réponse est attendu, que le message est chiffré et qu'il est authentifié.
  - `msgSecurityModel: USM (3)` -> Indique le modèle de sécurité utilisé ici USM (User-Based Security Model).
- `msgAuthoritativeEngineID: 800<...>362b` -> Indique l'ID d'identification unique du moteur SNMP.
  - `Engine ID Conformance: RFC3411 (SNMPv3)` -> Indique que l'ID est conforme à la norme RFC3411.
  - `Engine Enterprise ID: ciscoSystems (9)` -> Indique l'identifiant de l'entreprise qui a fabriqué le produit.
  - `Engine ID Format: MAC address (3)` -> Indique que le format de ID est une MAC.
  - `Engine ID Data: Cisco type: Agent (0x00)` -> Indique que c'est un agent.
  - `Engine ID Data: MAC address: <...>` -> Indique l'adresse MAC.
- `msgAuthoritativeEngineBoots: 6` -> Indique le nombre de fois que le moteur SNMP à redémarré depuis ça configuration.
- `msgAuthoritativeEngineTime: 1707` -> Indique le temps depuis écoulé depuis le dernier redémarrage.
- `msgUserName: secureuser` -> Indique le nom de l'utilisateur qui a généré le message.
- `msgAuthenticationParameters: 652<...>2f3` -> Indique les paramètres d'authentification utilisé.
- `msgPrivacyParameters: 000<...>f08` -> Indique les paramètres utilisé pour chiffrer le message.
- `msgData: encryptedPDU (1)` -> Indique le type de message PDU envoyé, ici chiffré. 
  - `encyptedPDU: e233<...>2a12` -> Indique le PDU chiffré qui contient le message.



---

> 20. Quelle(s) bonne(s) pratique(s) supplémentaires suggérez-vous pour sécuriser votre trafic SNMP v3 ?

#### **Réponse:**

- Utiliser des mots de passe forts pour plus de sécurité.
- Configurer le routeur pour limiter les adresses IP ayant accès à SNMP.
- Surveiller le trafic SNMP pour détecter les activité suspecte.
- Désactiver SNMPv1 et SNMPv2.
- Faire une rotation periodique des mots de passe.
- Utiliser les vues pour limiter l'accès aux objets SNMP aux strict nécessaire.
- Utiliser un VLAN dédié pour le traffic SNMPv3.

---



## Objectif 6 : Utilisation de WMI

Vérifiez que le service WMI est fonctionnel sur votre machine Windows.
Pour des questions de simplification de l’infrastructure du laboratoire, la console WMI et l’agent WMI se trouvent sur la même machine.  
L’accès à un agent WMI distant nécessite le réglage des autorisations, notamment au niveau DCOM.

![alt text](image-1.png)


- A l’aide de WMI explorer, retrouver les caractéristiques du processeur de votre VM
Windows ainsi que le SID de votre utilisateur local.

---

> 21. Montrez le résultat avec une capture d’écran.


#### **Réponse:**

Les caractéristiques processeur:

![alt text](image-q21.png)

Le SID de l'utilisateur local:

![alt text](image-q21-2.png)


---


- Ecrivez un script PowerShell permettant de lister, à l’aide de WMI, les partitions de la VM Windows avec leur lettre de lecteur et de retourner le pourcentage d’espace vide.
En cas d’espace insuffisant, une alarme Syslog est générée et récupérée sur votre serveur Syslog.

---
> 22. Montrez votre script.


#### **Réponse:**

```PowerShell
Import-Module Posh-SYSLOG
$threshold = 25
$partitions = Get-WmiObject -Class Win32_LogicalDisk -Filter "DriveType=3"

foreach ($partition in $partitions) {
    if($partition.Size -gt 0){
        $freePct = [math]::Round(($partition.FreeSpace / $partition.Size) * 100, 2)
    
        Write-Host "Drive: $($partition.DeviceID)  $($freePct)% Free `n`r    Free:  $($partition.FreeSpace) `n`r    Total: $($partition.Size)"

        if ($freePct -lt $threshold) {
            $msg = @{
                Server = "127.0.0.1"
                Port = 514
                Facility = 16
                Severity = 4
                Message = "Low free space on drive $($partition.DeviceID)  $freePct % left."
            }
            Send-SyslogMessage @msg
        }
    }
}
```





---

> 23. Montrez le résultat (valeurs obtenues et message Syslog reçu).


#### **Réponse:**


![alt text](image-39.png)

Pour obtenir une sortie syslog, nous avons temporairement modifié le threshold, pour une valeur de 80%.

![alt text](image-40.png)


---


- Etablissez une souscription permanente à un événement lorsqu’un périphérique USB est
inséré dans votre système. Une notification est visible dans l’observateur d’événements. 

---
> 24. Montrez votre commande.


#### **Réponse:**

```PowerShell
# WMI USB Insert logger
New-EventLog -LogName "USBInsertEvent" -Source "USBLogger"

$query = "SELECT * FROM Win32_VolumeChangeEvent WHERE EventType=2"
Register-WmiEvent -Query $query -Action {
    Write-EventLog -LogName "USBInsertEvent" -Source "USBLogger" -EventId 1 -Message "USB inserted: $($Event.SourceEventArgs.NewEvent.DriveName)"
}
```


---

> 25. Montrez l’événement reçu.


#### **Réponse:**


![alt text](image-41.png)

---
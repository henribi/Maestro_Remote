# maestro remote

## INTRODUCTION ET REMERCIEMENTS
Ce programme permet de commander un poêle à granule, basé sur la technologie Maestro, en s'interfaçant avec les serveurs MCZ.

Il n'y a pas de dialogue direct avec le poêle

Il est très grandement basé sur le travail de Anthony et EtienneME sur le forum suivant: https://community.jeedom.com/t/mcz-maestro-et-jeedom/6159/183  
Git original: https://github.com/Anthony-55/maestro  
J'ai créé ce git pour aider certaines personnes qui le demandaient, je ne souhaite en aucun cas m'approprier le code qui n'est pas le mien, et sans lequel ce programme n'existerait pas.

Ce programme, bien que fonctionnel, est tout sauf optimisé (code approximatif, code restant non utilisé du travail sur lequel il est basé.) N'hésitez pas à l'améliorer et à contribuer ;)  
Le *changelog* donne les différentes évolutions du code.

## INSTALLATION
Ce programme nécéssite :
* Python3
* paho-mqtt
* websocket (code à supprimer pour s'en passer)
* Socket.io python client: https://python-socketio.readthedocs.io/en/latest/client.html

Pour l'installer (même procédure que celle présentée par Anthony sur son git)
```sh
git clone https://github.com/pipolaq/maestro.git
cd maestro
```

Ensuite, modifiez le fichier "\_config\_.py". Gardez bien les double quote


Ensuite :

```
sudo bash install_daemon
```



Pour démarrer le daemon : :
```sh
sudo systemctl start maestro.service
```

Pour activer le lancement automatique au démarrage du système :
```sh
sudo systemctl enable maestro.service 
```

Pour finir, il arrive que le programme plante (communication avec les serveurs MCZ ou bug du broker MQTT). J'ai donc un scenario qui vérifie toutes les 10 minutes la date de la dernière remontée d'information, et si celle-ci est supérieure à 4 minutes, le scenario déclenche un 
```
systemctl restart maestro.service
```

## LOGS

Les fichiers de log se trouvent dans le directory /var/log/maestro


## COMMANDES DU POELE
Ce sont les commandes qu'il faut envoyer au poele pour le controler.
Cette liste n'est pas exhaustive. Il y a plus d'informations dans le forum à l'url :
https://community.jeedom.com/t/mcz-maestro-et-jeedom/6159/183

```
Allumer le poêle : 34,1
Éteindre le poêle : 34,40

Passage Mode Eté/Hiver :
    Eté     : 58,1
    Hiver   : 58,0

Mode ACTIVE :
    Activé      : 35,1
    Désactivé   : 35,0
    Reset du mode ACTIVE : 2,255 

Activer les sons du poêle : 50,1
Desactiver les sons du poêle : 50,0

Activer le mode ECO : 41,1
Désactiver le mode ECO : 41,0

Désactiver le thermostat interne (c’est par exemple jeedom qui régulera les on et off) : 40,0
Régulation de température sur sonde (sonde interne) : 40,1

Niveau de puissance 1 : 36,1
Niveau de puissance 2 : 36,2
Niveau de puissance 3 : 36,3
Niveau de puissance 4 : 36,4
Niveau de puissance 5 : 36,5

Chrono ON : 1111,1
Chrono OFF : 1111,0

Modifier température consigne mode Chrono :
    T1 : 1108,xx ou xx est la consigne demandée (nombre entier compris entre 5 et 35)
    T2 : 1109,xx
    T3 : 1110,xx


Ventilateur ambiance
    Niveau 1  : 37,1
    Niveau 2  : 37,2
    Niveau 3  : 37,3
    Niveau 4  : 37,4
    Niveau 5  : 37,5
    Automatique : 37,6
    
Ventilateur canalisé 1
    Niveau 1  : 38,1
    Niveau 2  : 38,2
    Niveau 3  : 38,3
    Niveau 4  : 38,4
    Niveau 5  : 38,5
    Automatique : 38,6
    
Ventilateur canalisé 2
    Niveau 1  : 39,1
    Niveau 2  : 39,2
    Niveau 3  : 39,3
    Niveau 4  : 39,4    
    Niveau 5  : 39,5
    Automatique : 39,6

Profil
    Manuel    : 149,0
    Dynamic   : 149,1
    Overnight : 149,2
    Confort   : 149,3
    Power     : 149.4
    Manuel Adaptatif    : 149,10
    Dynamic Adaptatif   : 149,11
    Overnight Adaptatif : 149,12
    Confort Adaptatif   : 149,13
    Power Adaptatif     : 149.14

    Remarque: Les modes adaptatif sont reconnus comme "code inconnu".

Consigne de température:  42,xx  avec xx le double de la température pour supporter les demi degré

Mise à jour de la date et heure du poêle. (nécessite cette version du script)
    9001,DDMMYYYYHHmm
    exemple:  9001,011120212010      pour 01/11/2021 20h10
`

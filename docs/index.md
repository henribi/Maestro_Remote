# maestro remote

## INTRODUCTION ET REMERCIEMENTS
Ce programme permet de commander un poêle à granule, basé sur la technologie Maestro, en s'interfaçant avec les serveurs MCZ.

Il n'y a pas de dialogue direct avec le poêle

> **Prérequis**
>
>Votre poêle doit être connecté au wifi de votre domicile. Il doit pouvoir être contrôlé par l'application MCZ Maestro à partir de votre smartphone en 4G ou hors de votre domicile.
>
>> ***Attention***
>>
>> Ce script n'est pas compatible avec les poêles qui utilisent l'app Maestro MCZ et le protocole Maestro+
>>
>>


Il est très grandement basé sur le travail de Anthony et EtienneME sur le forum suivant: <https://community.jeedom.com/t/mcz-maestro-et-jeedom/6159/183>  
Git original: <https://github.com/Anthony-55/maestro>  
Ce git a été créé pour aider, je ne souhaite en aucun cas m'approprier ce code qui n'est pas le mien, et sans lequel ce programme n'existerait pas.

Ce programme, bien que fonctionnel, est tout sauf optimisé (code approximatif, code restant non utilisé du travail sur lequel il est basé.) N'hésitez pas à l'améliorer et à contribuer ;)  
Le *changelog* donne les différentes évolutions du code.

## INSTALLATION
Ce programme nécéssite :
* Python3
* paho-mqtt
* websocket (code à supprimer pour s'en passer)
* Socket.io python client: <https://python-socketio.readthedocs.io/en/latest/client.html>

Pour l'installer (même procédure que celle présentée par Anthony sur son git)
```sh
git clone https://github.com/henribi/Maestro_Remote.git
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

## DOCS - CHANGELOG

La doc et ainsi que le changelog sont disponibles aux url ci-dessous

Doc:        <https://henribi.github.io/Maestro_Remote/>

Changelog:  <https://henribi.github.io/Maestro_Remote/changelog>


## COMMANDES DU POELE
Ce sont les commandes qu'il faut envoyer au poele pour le controler.
Cette liste n'est pas exhaustive. Il y a plus d'informations dans le forum à l'url :
https://community.jeedom.com/t/mcz-maestro-et-jeedom/6159/183


Allumer/Eteindre
  - Allumer:  34,1  
  - Éteindre: 34,40  

Passage Mode Eté/Hiver :
  - Eté     : 58,1
  - Hiver   : 58,0

Mode ACTIVE :
  - Activé      : 35,1
  - Désactivé   : 35,0
  - Reset du mode ACTIVE : 2,255 

Sons du poêle : 
  - Activé:     50,1
  - Desactiver: 50,0

Mode ECO 
  - Activer:    41,1
  - Désactiver: 41,0

Désactiver le thermostat interne (c’est par exemple jeedom qui régulera les on et off) : 40,0

Régulation de température sur sonde (sonde interne) : 40,1

Puissance
  - Niveau 1 : 36,1
  - Niveau 2 : 36,2
  - Niveau 3 : 36,3
  - Niveau 4 : 36,4
  - Niveau 5 : 36,5   (Power 110%)

ChronoThermostato
  - ON : 1111,1
  - OFF : 1111,0

Modifier température consigne mode Chrono :
  - T1 : 1108,xx ou xx est la consigne demandée (nombre entier compris entre 5 et 35)
  - T2 : 1109,xx
  - T3 : 1110,xx


Ventilateur ambiance
  - Niveau 1  : 37,1
  - Niveau 2  : 37,2
  - Niveau 3  : 37,3
  - Niveau 4  : 37,4
  - Niveau 5  : 37,5
  - Automatique : 37,6
    
Ventilateur canalisé 1
  - Niveau 1  : 38,1
  - Niveau 2  : 38,2
  - Niveau 3  : 38,3
  - Niveau 4  : 38,4
  - Niveau 5  : 38,5
  - Automatique : 38,6
    
Ventilateur canalisé 2
  - Niveau 1  : 39,1
  - Niveau 2  : 39,2
  - Niveau 3  : 39,3
  - Niveau 4  : 39,4    
  - Niveau 5  : 39,5
  - Automatique : 39,6

Profil
  - Manuel    : 149,0
  - Dynamic   : 149,1
  - Overnight : 149,2
  - Confort   : 149,3
  - Power     : 149.4
  - Manuel Adaptatif    : 149,10
  - Dynamic Adaptatif   : 149,11
  - Overnight Adaptatif : 149,12
  - Confort Adaptatif   : 149,13
  - Power Adaptatif     : 149.14

>  **Remarque**:
>  Les modes adaptatif sont reconnus comme "code inconnu".
>

Consigne de température    
  42,xx  avec xx le double de la température pour supporter les demi degré  

Mise à jour de la date et heure du poêle. (nécessite cette version du script)  
  9001,DDMMYYYYHHmm  
  exemple:  9001,011120212010      pour 01/11/2021 20h10


## VERIFICATION DE FONCTIONNEMENT et RECUPERATION

Il arrive que le script se bloque ou que la connexion avec MCZ soit perdue. Les informations du poêle ne sont plus mise à jour. Il est facile de détecter ce cas et de relancer le script.

### Suivi du fonctionnement

J'utilise dans Jeedom un scenario programmé qui s'exécute toutes les 5 minutes ( \*/5 \* \* \* \* )

Ce scenario contient le bloc code ci-dessous.

```
// Recupere current time comme timestamp
$ts_curtime = time();
$curtime = date("d/m/Y H:i:s", $ts_curtime);
$scenario->setLog('=== ts_curtime: ' . $ts_curtime . ' =.=  '. $curtime);

// Recupere last collectDate of PUBmcz command within MQTT
$cmd = cmd::byString('#[Chauffage][PUBmcz][PUBmcz]#');   // <--- nom de commande à adapter 
$cmd->execCmd();
$collectDate = $cmd->getCollectDate();
$ts_collectdate = strtotime($collectDate);
$scenario->setLog('=== ts_collect: ' . $ts_collectdate . ' =.=  '. date("d/m/Y H:i:s", $ts_collectdate) );

//comparaison ...
if (($ts_curtime - $ts_collectdate) > 300) {
  $scenario->setLog('!!!!!  La mise à jour de maestro a un problème  ===>  Lancement du script de restart');
    log::add('scenario','info', '!!!!!  La mise à jour de maestro a un problème  ===>  Lancement du script de restart');
    cmd::byString('#[Chauffage][Maestro][restart]#')->execCmd();  // <--- nom de commande script à adapter  
}
```

### Récupération

La dernière ligne du bloc code lance un script qui est défini dans le plugin script telle que ci-dessous.

![Script](../images/script.png)

Le contenu du script /var/www/html/plugins/script/data/restartmaestro.sh contient ceci

```
#!/bin/bash

sudo systemctl restart maestro
```

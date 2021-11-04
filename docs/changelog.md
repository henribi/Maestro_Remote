# Changelog maestro remote

>**IMPORTANT**
>
>Pour rappel s'il n'y a pas d'information sur la mise à jour, c'est que celle-ci concerne uniquement de la mise à jour de documentation, de traduction ou de texte


# 04/11/2021
- dans maestro.service, modification du type vers simple
- suppression de appels thread (memory leak !!)
- déplacement des fichiers de log vers /var/log/maestro
- ajout dans la fonction "on_message_mqtt" du support pour le changement d'heure

# pre 30/10/2021
- basé sur les travaux de Anthony L., EtienneME, SkyPhil, Pipolas
- déplacement dans le fichier \_config\_.py file des parametres du poele


# Introduction à Docker

``docker run image_name`` : permet de créer et démarrer une nouvelle instance de l'image (conteneur) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                              si l'image n'existe pas, elle sera téléchargée à partir de docker hub
                        
``docker run -d image_name:tag`` : permet de démarrer le conteneur en arrière plan 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                              tag : version; la valeur par défaut d'une image est lastest

``docker ps`` : permet de lister tous les conteneurs qui sont en cours d'exécution. Chaque conteneur dispose d'un identifiant unique

``docker ps -a`` : permet de lister tous les conteneurs avec leurs status (Up, Exited, Created)

 ``docker run -d -p 9999:80 nginx`` : Executer une image du serveur nginx

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                              -p => redirection de port *port-extérieur-du-monteur-Docker* **:** *port-intérieur-du-conteneur*

``docker run --name <NAME> -d -p 8989:80 nginx`` : **--name** permet de donner un nom à mon conteneur

``docker start <hash-du-conteneur|name-du-conteneur>`` : permet de démarrer un conteneur

``docker stop <hash-du-conteneur|name-du-conteneur>`` : permet d'arrêter un conteneur

``docker rm <hash-du-conteneur>`` : permet de supprimer le conteneur, 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                                    **Attention** on ne peut pas supprimer un conteneur en cours d'exécution, il faut le stopper avant

``docker images`` : permet de lister toutes les images

``docker rmi -f <nom-image>`` : forcer la suppression d'une image ou d'un conteneur

``exec`` : permet d'executer une commande dans le conteneur démarré

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

### Exercice 1, objectif : démarrer un conteneur MySQL et executer le client mysql
``docker run --name <NAME> -e MYSQL_ROOT_PASSWORD=root -d mysql`` : démarrer le conteneur MySQL

``docker exec -ti <NAME> mysql --password`` : exécuter le client mysql

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

### Exercice 2, objectif : démarer un conteneur à partir de l'image ubuntu et puis installer java et exécuter un programme simple
``docker run -it ubuntu bash``
Une fois à l'intérieur du conteneur, on peut installer les outils dont on a besoin (avec les commandes ubuntu).

Si on veut installer par exemple Java, on procède de la façon suivante : on met d'abord à jour ``apt-get update`` et puis ``apt-get install java``. 

Si on veut utiliser vim comme éditeur ``apt-get install vim``.

``docker logs id_conteneur`` : permet d'avoir les logs d'un contenur

Html à modifier : ``/usr/share/nginx/html``

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

### Exercice 3 : démarrer un conteneur nginx et changer le contenu du fichier index.html.
Notion de volume :

``docker volume``

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes

``docker volume create monVolume`` : création du volume

``docker volume inspect monVolume`` : inspection du volume

### Mapping volume
Pour faire "mapping volume" :

Créer dans **C:\Users\jallo\Documents\docker\backupNginx** un fichier *index.html* 

``docker run -dti -p 8081:80 -v C:\Users\jallo\Documents\docker\backupNginx:/usr/share/nginx/html/ --name myServWeb nginx``

ou 

``docker run -tid --name webvolume -p 8082:80 --mount source=monvolume,target=/usr/share/nginx/html/ nginx``


Pour accéder aux volumes (sous mac & windows) :

``docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh``

Une fois à l'intéreiur du conteneur, les volumes se trouvent dans le dossier : ``/var/lib/docker/volumes/``

Solution ici : https://stackoverflow.com/questions/38532483/where-is-var-lib-docker-on-mac-os-x

## Récapitulatif :
``docker volume create mydata``
``docker run -tid --name webvolume2 -p 8085:80 --mount source=mydata,target=/usr/share/nginx/html/ --name servRecap nginx``


### Gestion des variables d'environnement :

``docker run -tid --name variableenvironnement --env MAVARIABLE="jallon2u" ubuntu:latest``

Passer une variable d'environnemnt en ligne de commande : ``docker exec -ti variableenvironnement sh``

On créé un fichier **variables.lst** avec vim

On passe un ensemble de variables d'environnement sauvegardé dans le fichier variables.lst :

``docker run -tid --name fichiervariableenvironnement --env-file variables.lst ubuntu:latest``


### Définir sa propre image Docker :

    ``docker commit``

Exemple : partir d'une image ubuntu, on va installer python et vim et ajouter queqlues variables d'environnement sauvegardées dans un fichier.

1 - On commence par créer un fichier env.lst (on définit autant de variables souhaitées)

2 - Pour ajouter ces variables d'environnement ``docker run --name monconteneurvariables -tdi --env-file env.lst ubuntu``

3 - Pour installer python et vim, on rentre à l'intérieur du conteneur ``docker exec -ti monconteneurvariables sh``

Pour installer python et vim : ``apt-get update``, ``apt-get install python`` et ``apt-get install vim``

4 - On quite le conteneur ``exit``

5 - Pour créer une image à partir de ce conteneur ``docker commit -m "création d'une image ubuntu augumentée" id_conteur nom_notre_image``

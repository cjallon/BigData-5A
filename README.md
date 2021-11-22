# Introduction à Docker

docker run image_name : permet de créer et démarrer une nouvelle instance de l'image (conteneur)
                        si l'image n'existe pas, elle sera téléchargée à partir de docker hub
                        
docker run -d image_name:tag : permet de démarrer le conteneur en arrière plan
                              tag : version; la valeur par défaut d'une image est lastest

docker ps : permet de lister tous les conteneurs qui sont en cours d'exécution. Chaque conteneur dispose d'un identifiant unique

docker ps a : permet de lister tous les conteneurs avec leurs status (Up, Exited, Created)

Executer une image du serveur nginx : docker run -d -p 9999:80 nginx
                                      -p => redirection de port *port-extérieur-du-monteur-Docker*:*port-intérieur-du-conteneur*

''docker run --name <NAME> -d -p 8989:80 nginx'' : **--name** permet de donner un nom à mon conteneur

''docker stop <hash-du-conteneur|name-du-conteneur>'' : permet d'arrêter un conteneur

''docker rm <hash-du-conteneur>'' : permet de supprimer le conteneur, /!\Attention/!\ on ne peut pas supprimer un conteneur en cours d'exécution, il faut le stopper avant

''docker images'' : permet de lister toutes les images

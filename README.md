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



### Dockerfile

1 - On va créer un fichier Dockerfile 

FROM : permet de définir l'image source
CMD : la commande par défautt lors de l'exécution du conteneur
EXPOSE : permet de définir le port d'écoute par défaut
RUN : permet d'exécuter des commandes à l'intérieur du conteneur
ADD : permet d'ajouter des fichier dans le conteneur
VOLUME : permet de définir les volumes utilisables

2 - On part d'une image existante (la plus petite est alpine), copier éventuellement un programme et l'exécuter.

  FROM openjdk
  COPY Application.class Application.class
  CMD ["java", "Application"]
  
#### Exemple pour Python :

  FROM ubuntu
  COPY application.py application.py
  RUN apt-get update
  RUN apt-get install python -y
  CMD ["python", "application.py"]





# MongoDB





# Exercice à rendre

*Développer un micro-service qui utilise pour la persistance des données MongoDB et créer une image Docker de votre service.*

##### Étape 1 : Création du service

A l'aide d'Intellij Idea on va réaliser un projet Spring. On va utiliser les deux dépendances suivantes : **Spring Web** et **MongoDB**.

Dans un premier temps nous allons créer une interface implémentant **MongoRepository** que l'on va appeler Produit :

```java
package com.example.deeplearning;

import org.springframework.data.mongodb.repository.MongoRepository;

public interface ProduitInterface extends MongoRepository<Produit, String> {

    Produit findByDescription(String description);
}
```

Classe produit :
```java
package com.example.deeplearning;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;
import java.io.Serializable;

@Document(collection = "produit")
public class Produit implements Serializable {

    @Id
    private String id;

    @Field("nom")
    private String nom;

    @Field("prix")
    private double prix;

    public Produit(String id, String nom, double prix){
        this.id = id;
        this.nom = nom;
        this.prix = prix;
    }
}
```

**@Document(collection = "produit")** : correspond à la collection utilisée
**@Id** : id du document, généré automatiquement
**@Field("nom")** : indication que l'attribut est converti en tant que clé/vlaleur nommée **nom** dans les objets de la collecton produit de MongoDB.

L'étape suivante consiste à réaliser le Mapping qui va s'occuper de répondre à nos requêtes HTTP. Le contrôleur/service s'appelera ServiceProduit :

```java
package com.example.bigdata;

import com.example.bigdata.Produit;
import com.example.bigdata.ProduitInterface;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Collection;

@RestController
@RequestMapping("/produit")
public class ServiceProduit {
    private final ProduitInterface monProduit;

    public ServiceProduit(ProduitInterface monProduit){
        this.monProduit = monProduit;

    }

    @GetMapping("/{description}/{prix}")
    public Produit add(@PathVariable String description, @PathVariable double prix)
    {
        if(!description.isEmpty())
        {
            return monProduit.insert(new Produit(null, description, prix));
        }
        else
        {
            return null;
        }
    }

    @GetMapping("/{description}")
    public Produit getById(@PathVariable String description )
    {
        return monProduit.findByDescription((description));
    }

    @GetMapping
    public Collection<Produit> get() {
        return monProduit.findAll();
    }
}
```

Dans ce contrôleur on utilise du GetMapping (GET). De plus, afin de pouvoir interagir avec la base de données MongoDB, on va devoir instancier ProduitInterface que l'on a créé précédemment.

Dans notre projet, nous utilisons les méthodes suivantes:
```
  findAll(): permet de renvoyer l'intégralité des objets dans la collection produit
  insert(Produit p): permet d'insérer un nouvel objet dans la base de données
  findByDescription(description): permet de faire une recherche sur la collection produit pour trouver un objet possédant une description identique à description
```


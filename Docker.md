# Docker 🐳

- [Docker 🐳](#docker-)
  - [Débuter avec Docker avec des images existantes](#débuter-avec-docker-avec-des-images-existantes)
    - [Introduction](#introduction)
    - [Pré requis](#pré-requis)
    - [Vérifier l’installation](#vérifier-linstallation)
    - [Télécharger une image légère](#télécharger-une-image-légère)
    - [Exécuter un conteneur simple](#exécuter-un-conteneur-simple)
    - [Conteneur interactif](#conteneur-interactif)
    - [Créer plusieurs conteneurs à partir de la même image](#créer-plusieurs-conteneurs-à-partir-de-la-même-image)
    - [Isolation des conteneurs](#isolation-des-conteneurs)
    - [Exécuter une commande dans un conteneur existant](#exécuter-une-commande-dans-un-conteneur-existant)
    - [Nettoyer les conteneurs](#nettoyer-les-conteneurs)
    - [Points clés à retenir](#points-clés-à-retenir)
  - [Tableau récapitulatif des commandes Docker](#tableau-récapitulatif-des-commandes-docker)
  - [Liens utiles](#liens-utiles)

## Débuter avec Docker avec des images existantes

**Objectif :** Découvrir Docker, manipuler des images et conteneurs, créer plusieurs instances, gérer l’isolation et nettoyer les conteneurs.

### Introduction

**Pourquoi Docker ?**  

Docker est une technologie de conteneurisation qui permet de **packager une application et toutes ses dépendances** dans un environnement léger et isolé appelé *conteneur*. Contrairement aux machines virtuelles qui virtualisent le hardware complet, Docker fonctionne au niveau applicatif, ce qui le rend **rapide et efficace en ressources**.

**En entreprise :**

- **Déploiement rapide d’applications** : vos équipes peuvent lancer une application sur n’importe quelle machine sans se soucier des différences de configuration.  
- **Isolation et sécurité** : chaque application tourne dans son propre conteneur, évitant les conflits avec d’autres services.  
- **Reproductibilité** : ce qui fonctionne sur votre machine fonctionne exactement de la même manière sur un serveur ou en production.  

**Exemples orientés Data :**

- Déployer rapidement un **serveur PostgreSQL ou MySQL** dans un conteneur pour tester des pipelines de données.  
- Lancer des **jobs Python ou R** avec toutes les librairies nécessaires préinstallées (pandas, numpy, scikit-learn, etc.) sans polluer votre machine locale.  
- Tester des outils comme **Airflow, Spark ou MinIO** dans des conteneurs pour construire et orchestrer des pipelines de données.  
- Faciliter la collaboration : chaque membre de l’équipe peut exécuter exactement les mêmes conteneurs, assurant la **cohérence des environnements**.  

:bulb: En résumé, Docker permet de travailler plus vite, plus proprement et de manière reproductible, ce qui est **indispensable dans les projets data modernes** où plusieurs outils et services doivent coexister.

### Pré requis

- Créer un compte sur Docker Hub : [https://hub.docker.com](https://hub.docker.com)  
  *Cela permettra de télécharger des images et d'utiliser les labs en ligne docker.*  
- Aller sur [Play with Docker](https://labs.play-with-docker.com/)  
  *Environnement gratuit en ligne pour tester Docker sans installation locale.*  
- Créer une instance sur Play with Docker  
  *Chaque instance est un mini serveur Linux où vous pourrez lancer vos conteneurs.*


### Vérifier l’installation

Assurez-vous que Docker fonctionne en tapant `docker version` et `docker info`.  

```bash
docker version
docker info
```

Test rapide avec l’image de test :

```bash
docker container run hello-world
```

- Docker cherche l’image `hello-world` localement.  
- Si elle n’existe pas, elle est téléchargée depuis Docker Hub.  
- Le conteneur s’exécute, affiche un message, puis se ferme automatiquement.

### Télécharger une image légère

Téléchargez l’image Alpine Linux (très légère) :

```bash
docker image pull alpine
```

Listez les images locales avec `docker image ls`.  

```bash
docker image ls
```

Les colonnes importantes sont : `REPOSITORY` (nom de l’image), `TAG` (version), `IMAGE ID` (identifiant unique) et `SIZE` (taille).

### Exécuter un conteneur simple

Lister les fichiers à l’intérieur d’Alpine :

```bash
docker container run alpine ls -l
```

Afficher un message simple :

```bash
docker container run alpine echo "hello from alpine
```


Chaque commande `run` crée un nouveau conteneur éphémère qui s’arrête après l’exécution.

### Conteneur interactif

Lancer un conteneur avec un shell interactif : 

```bash
docker container run -it alpine /bin/sh
```

À l’intérieur, tester des commandes comme `ls -l` ou `uname -a`.  
Pour quitter, taper `exit`.

### Créer plusieurs conteneurs à partir de la même image

- Conteneur 1 :
 
```bash
docker container run -it alpine /bin/sh
```


- Conteneur 2 :  

```bash
docker container run -it alpine /bin/sh
```


- Conteneur 3 : créer un fichier dans le containeur

```bash
docker container run -it alpine /bin/sh
echo "hello world" > hello.txt
exit
```


Vérifiez tous les conteneurs existants (y compris arrêtés) avec `docker container ls -a`.

```bash
docker container ls -a
```

### Isolation des conteneurs

Si vous relancez un nouveau conteneur Alpine et tapez `ls`, vous ne verrez pas `hello.txt`.  
- Chaque conteneur est isolé.  
- Les fichiers et modifications d’un conteneur ne sont pas visibles dans un autre conteneur.

### Exécuter une commande dans un conteneur existant

Pour voir le contenu de `hello.txt` dans le conteneur où il a été créé :  

- Redémarrer le conteneur avec `docker container start -i <container_id>`  
- Lister les fichiers : `ls`  
- Lire le contenu : `cat hello.txt`

```bash
docker container start -i <container_id>
ls
cat hello.txt
exit
```

### Nettoyer les conteneurs

Lister les conteneurs actifs :

```bash
docker container ls
```

Lister tous les conteneurs :

```bash
docker container ls -a
```

Pour arrêter un conteneur actif :

```bash
docker container kill <container_id>
```


Pour supprimer un conteneur arrêté :  

```bash
docker container rm <container_id>
```


### Points clés à retenir

- Une **image** est un modèle figé contenant un OS et des dépendances.  
- Un **conteneur** est une instance en cours d’exécution de cette image.  
- Les conteneurs sont **éphémères** et **isolés**.  
- Docker est rapide car il fonctionne au niveau applicatif, contrairement aux VM qui virtualisent le hardware.  
- Les images par défaut proviennent de **Docker Hub**, mais vous pouvez en utiliser d’autres.  


## Tableau récapitulatif des commandes Docker

| Commande | Description |
|----------|-------------|
| `docker version` | Affiche la version du client et du serveur Docker |
| `docker info` | Affiche les informations détaillées sur l’installation Docker |
| `docker container run hello-world` | Télécharge (si nécessaire) et exécute le conteneur de test "hello-world" |
| `docker image pull alpine` | Télécharge l’image Alpine Linux depuis Docker Hub |
| `docker image ls` | Liste toutes les images Docker présentes localement |
| `docker container run alpine ls -l` | Exécute la commande `ls -l` dans un nouveau conteneur Alpine |
| `docker container run alpine echo "hello from alpine"` | Exécute la commande `echo` dans un nouveau conteneur Alpine |
| `docker container run -it alpine /bin/sh` | Lance un conteneur Alpine en mode interactif avec un shell |
| `docker container ls` | Liste les conteneurs en cours d’exécution |
| `docker container ls -a` | Liste tous les conteneurs, même ceux arrêtés |
| `docker container start -i <container_id>` | Redémarre un conteneur existant et ouvre un shell interactif |
| `docker container exec <container_id> <commande>` | Exécute une commande dans un conteneur en cours d’exécution |
| `docker container kill <container_id>` | Arrête immédiatement un conteneur actif |
| `docker container rm <container_id>` | Supprime un conteneur arrêté |
| `cat hello.txt` | Affiche le contenu du fichier `hello.txt` à l’intérieur du conteneur |
| `ls` | Liste les fichiers/dossiers à l’intérieur du conteneur |
| `echo "texte" > hello.txt` | Crée un fichier `hello.txt` contenant le texte "texte" à l’intérieur du conteneur |

## Liens utiles

- [Tutoriel Docker](https://training.play-with-docker.com/)
- [Docker Hub](https://www.docker.com/)
- [Play with docker](https://labs.play-with-docker.com/)
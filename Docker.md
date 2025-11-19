# Docker 🐳

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Docker_%28container_engine%29_logo.svg/1280px-Docker_%28container_engine%29_logo.svg.png" alt="Source de l'image" width="600"/>
</p>

- [Docker 🐳](#docker-)
- [Comprendre Docker et la notion de conteneurs](#comprendre-docker-et-la-notion-de-conteneurs)
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
- [Installer Docker sur Windows et comprendre ses composants](#installer-docker-sur-windows-et-comprendre-ses-composants)
  - [Installer Docker Desktop](#installer-docker-desktop)
    - [Vérification](#vérification)
  - [Rôle de Docker Desktop](#rôle-de-docker-desktop)
  - [WSL 2 (Windows Subsystem for Linux)](#wsl-2-windows-subsystem-for-linux)
  - [Pourquoi utiliser Docker sur Windows ?](#pourquoi-utiliser-docker-sur-windows-)
- [Construire une image existante avec interface graphique](#construire-une-image-existante-avec-interface-graphique)
  - [Objectif](#objectif)
  - [Pré-requis](#pré-requis-1)
  - [Pourquoi plutôt Docker ?](#pourquoi-plutôt-docker-)
  - [Streamlit](#streamlit)
    - [Créer l’application Streamlit](#créer-lapplication-streamlit)
    - [Créer le Dockerfile](#créer-le-dockerfile)
    - [Construire l’image Docker](#construire-limage-docker)
    - [Lancer le conteneur](#lancer-le-conteneur)
    - [En résumé](#en-résumé)
    - [Supprimer le conteneur](#supprimer-le-conteneur)
    - [Tableau récapitulatif des commandes](#tableau-récapitulatif-des-commandes)
    - [💡 Astuces](#-astuces)
- [Quelques notions de Réseau](#quelques-notions-de-réseau)
  - [La notion de port sur un PC :](#la-notion-de-port-sur-un-pc-)
  - [Les ports sur le web et en localhost](#les-ports-sur-le-web-et-en-localhost)
    - [En gros](#en-gros)
    - [Schéma récapitulatif](#schéma-récapitulatif)
- [Gestion des volumes Docker](#gestion-des-volumes-docker)
  - [Pourquoi utiliser des volumes ?](#pourquoi-utiliser-des-volumes-)
  - [Types de stockage](#types-de-stockage)
    - [Volumes nommés](#volumes-nommés)
    - [Bind mounts (montage de répertoire hôte)](#bind-mounts-montage-de-répertoire-hôte)
  - [Commandes utiles](#commandes-utiles)
    - [Créer un volume](#créer-un-volume)
    - [Lister les volumes](#lister-les-volumes)
    - [Inspecter un volume](#inspecter-un-volume)
    - [Supprimer un volume](#supprimer-un-volume)
  - [Gestion des volumes Docker avec des fichiers CSV](#gestion-des-volumes-docker-avec-des-fichiers-csv)
    - [Arborescence du projet](#arborescence-du-projet)
    - [Préparer un script Python](#préparer-un-script-python)
    - [Créer un fichier Dockerfile](#créer-un-fichier-dockerfile)
    - [Créer et monter un volume Docker](#créer-et-monter-un-volume-docker)
    - [Construire l’image Docker](#construire-limage-docker-1)
    - [Lancer le conteneur pour générer le CSV](#lancer-le-conteneur-pour-générer-le-csv)
    - [Vérifier la persistance](#vérifier-la-persistance)
    - [Lister les fichiers dans un volume nommé](#lister-les-fichiers-dans-un-volume-nommé)
    - [Explorer le volume en mode interactif](#explorer-le-volume-en-mode-interactif)
    - [Bonnes pratiques](#bonnes-pratiques)
  - [Générer un CSV avec Docker et monter un dossier local](#générer-un-csv-avec-docker-et-monter-un-dossier-local)
    - [Modifier le script `generate_csv.py`](#modifier-le-script-generate_csvpy)
    - [Préparer un dossier local pour stocker les CSV](#préparer-un-dossier-local-pour-stocker-les-csv)
    - [Construire l’image Docker](#construire-limage-docker-2)
    - [Lancer le conteneur Python avec un bind mount](#lancer-le-conteneur-python-avec-un-bind-mount)
    - [Vérifier le fichier sur l’hôte](#vérifier-le-fichier-sur-lhôte)
- [Introduction à Docker Compose](#introduction-à-docker-compose)
  - [Objectif](#objectif-1)
  - [Arborescence du projet](#arborescence-du-projet-1)
  - [Fichier app.py](#fichier-apppy)
  - [Fichier requirements.txt](#fichier-requirementstxt)
  - [Fichier Dockerfile](#fichier-dockerfile)
  - [Fichier docker-compose.yml](#fichier-docker-composeyml)
  - [Créer la table MySQL](#créer-la-table-mysql)
  - [Lancer toute l’application](#lancer-toute-lapplication)
  - [Vérifier les données](#vérifier-les-données)
  - [Nettoyer](#nettoyer)
- [Introduction à Docker Swarm Mode](#introduction-à-docker-swarm-mode)
  - [Découverte](#découverte)
  - [Docker Swarm Mode : qu’est-ce que c’est ?](#docker-swarm-mode--quest-ce-que-cest-)
    - [Rôles des nœuds](#rôles-des-nœuds)
    - [Intérêt](#intérêt)
  - [Déploiement et Scaling d’un Service Web](#déploiement-et-scaling-dun-service-web)
    - [Objectif](#objectif-2)
    - [Lancer l’environnement](#lancer-lenvironnement)
    - [Initialiser le Swarm](#initialiser-le-swarm)
    - [Vérifier les nœuds du cluster](#vérifier-les-nœuds-du-cluster)
    - [Créer un service web](#créer-un-service-web)
    - [Tester le service](#tester-le-service)
    - [Scaler (augmenter le nombre d’instances)](#scaler-augmenter-le-nombre-dinstances)
    - [Load Balancing Automatique](#load-balancing-automatique)
    - [Tolérance aux pannes](#tolérance-aux-pannes)
    - [Réduire le nombre d’instances](#réduire-le-nombre-dinstances)
    - [Compréhension du fonctionnement](#compréhension-du-fonctionnement)
      - [Swarm = un cluster de serveurs Docker](#swarm--un-cluster-de-serveurs-docker)
      - [Service = application distribuée](#service--application-distribuée)
      - [Scaling = augmenter la capacité](#scaling--augmenter-la-capacité)
      - [Load Balancing intégré](#load-balancing-intégré)
      - [Résilience](#résilience)
      - [Supprimer le service](#supprimer-le-service)
    - [En résumé](#en-résumé-1)
    - [Exemple visuel](#exemple-visuel)
    - [À retenir](#à-retenir)
  - [Formulaire Web Streamlit + MySQL  avec Play With Docker](#formulaire-web-streamlit--mysql--avec-play-with-docker)
    - [Objectif](#objectif-3)
    - [Préparer l’environnement PWD](#préparer-lenvironnement-pwd)
    - [Créer un réseau overlay](#créer-un-réseau-overlay)
    - [Déployer MySQL](#déployer-mysql)
    - [Préparer l’application Streamlit](#préparer-lapplication-streamlit)
      - [Créer `app.py`](#créer-apppy)
      - [Créer `requirements.txt`](#créer-requirementstxt)
      - [Créer `Dockerfile`](#créer-dockerfile)
    - [Construire l’image Streamlit](#construire-limage-streamlit)
    - [Déployer le service Streamlit sur le Swarm](#déployer-le-service-streamlit-sur-le-swarm)
    - [Tester l’application](#tester-lapplication)
    - [Nettoyer](#nettoyer-1)
    - [Conseils PWD](#conseils-pwd)
    - [Schéma visuel du TP](#schéma-visuel-du-tp)
- [Mini-projet Docker : Extraction des données DPE par département](#mini-projet-docker--extraction-des-données-dpe-par-département)
  - [Objectif pédagogique](#objectif-pédagogique)
  - [Consignes générales](#consignes-générales)
  - [Livrables attendus](#livrables-attendus)
  - [Points pédagogiques visés](#points-pédagogiques-visés)

# Comprendre Docker et la notion de conteneurs

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
- Tester des outils comme **Kafka, Airflow, Spark ou MinIO** dans des conteneurs pour construire et orchestrer des pipelines de données.  
- Faciliter la collaboration : chaque membre de l’équipe peut exécuter exactement les mêmes conteneurs, assurant la **cohérence des environnements**.  

:bulb: En résumé, Docker permet de travailler plus vite, plus proprement et de manière reproductible, ce qui est **indispensable dans les projets data modernes** où plusieurs outils et services doivent coexister.

### Pré requis

- Créer un compte sur Docker Hub : [https://hub.docker.com](https://hub.docker.com)  
  *Cela permettra de télécharger des images et d'utiliser les labs en ligne docker.*  
- Aller sur [Play with Docker](https://labs.play-with-docker.com/)  
  *Environnement gratuit en ligne pour tester Docker sans installation locale.*  
- Créer une instance sur Play with Docker  
  *Chaque instance est un mini serveur Linux où vous pourrez lancer vos conteneurs.*

:warning: Dans la console de **Play With Docker (PWD)** :  

- **Copier** : `CTRL + INSERT`  
- **Coller** : `SHIFT + INSERT`  

Contrairement aux raccourcis classiques (CTRL+C / CTRL+V), ces combinaisons fonctionnent directement dans les terminaux PWD pour éviter les conflits avec les commandes shell.

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
- Si elle n’existe pas, elle est téléchargée depuis Docker Hub avec 
```bash
docker pull hello-world:latest
docker container run hello-world
```
- Le conteneur s’exécute, affiche un message, puis se ferme automatiquement.

### Télécharger une image légère

Téléchargez l’image Alpine Linux (très légère) :

```bash
docker image pull alpine:latest
```

:bulb: Alpine est une **image Docker très légère** basée sur Linux.  

- Taille : ≈ 5 Mo → très rapide à télécharger et démarrer  
- Contient uniquement l’essentiel pour exécuter des commandes Linux  
- Idéale pour tester des services simples ou lancer des scripts courts  
- Moins de dépendances et donc moins de risques de bugs ou failles  

Dans ce TP, on utilise Alpine pour créer des containers légers qui dorment ou communiquent entre eux sans surcharger le cluster.

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
Chaque commande `run` crée un nouveau conteneur éphémère qui s’arrête après l’exécution.

### Conteneur interactif

Au lieu d'exécuter une commande directement avec `docker container run alpine ls -l`, il est aussi possible **d’entrer dans le conteneur** pour lancer `ls` (ou d’autres commandes) depuis l’intérieur.

1) Lancer un conteneur en mode interactif

On peut démarrer un conteneur avec un shell :

```bash
docker run -it alpine sh
```

2) Une fois à l’intérieur, il suffit d’utiliser la commande :

```bash
ls -l
```

À l’intérieur, on peut tester des commandes comme `ls -l` , `uname -a`, `mkdir toto`, etc.  

3) Pour sortir du conteneur :

```bash
exit
```

### Créer plusieurs conteneurs à partir de la même image

- Conteneur 1 :
 
```bash
docker container run alpine
```

- Conteneur 2 :  

```bash
docker container run alpine
```

- Conteneur 3 : créer un fichier dans le containeur

```bash
docker container run -it alpine /bin/sh
echo "hello world" > hello.txt
ls
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

# Installer Docker sur Windows et comprendre ses composants

Docker permet de créer et exécuter des conteneurs pour isoler des applications et leurs dépendances. Sur Windows, il est recommandé d’utiliser **Docker Desktop** pour simplifier l’installation et la gestion des conteneurs.


## Installer Docker Desktop

1. Télécharger Docker Desktop depuis le site officiel :  
   [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

2. Lancer l’installateur et suivre les étapes.

3. Redémarrer l’ordinateur si nécessaire.

### Vérification

Ouvrir PowerShell ou l’invite de commandes et taper :  
```shell
docker --version
docker compose version
```

Si Docker répond, l’installation est réussie.

## Rôle de Docker Desktop

- Fournit une **interface graphique** pour gérer les conteneurs et images.  
- Installe et configure automatiquement **le moteur Docker (Docker Engine)** sur Windows.  
- Intègre **Docker CLI** et **Docker Compose** pour travailler en ligne de commande.  
- Simplifie la configuration réseau et le partage de fichiers avec Windows.

## WSL 2 (Windows Subsystem for Linux)

Docker Desktop sur Windows fonctionne généralement avec **WSL 2**, un sous-système Linux intégré à Windows :

- Permet d’exécuter un noyau Linux complet sur Windows.  
- Les conteneurs Docker s’exécutent dans cet environnement Linux pour plus de compatibilité.  
- Nécessite l’activation de WSL 2 dans les fonctionnalités Windows et l’installation d’une distribution Linux (Ubuntu par exemple).

## Pourquoi utiliser Docker sur Windows ?

- Développer et tester des applications dans un environnement **identique à Linux**.  
- Isoler les projets pour éviter les conflits de dépendances.  
- Simplifier le déploiement d’applications sur d’autres machines ou serveurs.  
- Travailler avec Docker Compose pour orchestrer plusieurs conteneurs facilement.

# Construire une image existante avec interface graphique

## Objectif

Apprendre à manipuler Docker avec une application Streamlit, créer un conteneur, l’exécuter sur un port exposé, et gérer ses conteneurs.  
Le projet est léger et adapté à une VM de 4 Go.

## Pré-requis

- Créer un compte sur Docker Hub : [https://hub.docker.com](https://hub.docker.com)  
  *Cela permettra de télécharger des images et d'utiliser les labs en ligne Docker.*
- Aller sur [Play With Docker](https://labs.play-with-docker.com/)  
  *Environnement gratuit en ligne pour tester Docker sans installation locale.*
- Créer une instance sur Play With Docker  
  *Chaque instance est un mini serveur Linux où vous pourrez lancer vos conteneurs.*

## Pourquoi plutôt Docker ?

- Permet de **packager des applications avec toutes leurs dépendances**  
- Très utilisé en entreprise pour les **pipelines data**, les **microservices**, ou le **déploiement rapide**  
- Exemple Data : visualiser un dashboard web léger ou exécuter un modèle ML dans un conteneur isolé  

## Streamlit

<p align="center">
  <img src="https://streamlit.io/images/brand/streamlit-logo-secondary-colormark-darktext.png" alt="Source de l'image" width="600"/>
</p>

### Créer l’application Streamlit

1. Dans le terminal, créez un dossier pour l’application :  

```bash
mkdir streamlit_app
cd streamlit_app
```

2. Créez le fichier **app.py** :  

```bash
echo "import streamlit as st
st.title('Hello Docker!')
st.write('Ceci est une app Streamlit dans un conteneur Docker')
st.line_chart({'data': [1, 3, 2, 4, 5, 3]})
" > app.py
```

:warning:  Vérifier l’encodage de `app.py`
 - **VSCode** :
   - Ouvre `app.py`
   - En bas à droite, regarde l’encodage (UTF-8 ou UTF-16)
   - Si ce n’est pas UTF-8, clique dessus → `Reopen with Encoding` → `UTF-8`
   - Ensuite `File → Save with Encoding → UTF-8`

 - **Notepad++** :
   - `Encoding` → `Convert to UTF-8 (without BOM)` → sauvegarder

### Créer le Dockerfile

Un **Dockerfile** est un fichier texte qui décrit étape par étape comment construire l’image Docker d’une application.  
Il contient les instructions nécessaires pour créer un environnement reproductible, portable et prêt à exécuter l’application.

Voici un exemple de Dockerfile et l’explication de chaque ligne :

Dans le même dossier, créez le fichier **Dockerfile** manuellement ou avec : 

```bash
type nul > Dockerfile #sous Windows"
touch Dockerfile #sous Linux
```

Ouvrir le fichier et copier coller cette configuration : 

```Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY app.py /app
RUN pip install streamlit
EXPOSE 8080
CMD ["streamlit", "run", "app.py", "--server.port=8080", "--server.address=0.0.0.0"]
```

Voici l’explication de chaque ligne :

- `FROM python:3.11-slim`  : Choisit l’image de base. Ici, on utilise Python 3.11 dans une version légère appelée *slim*.  
C’est le point de départ de l’image.
- `WORKDIR /app` :  Définit le dossier de travail à l’intérieur du conteneur.  Toutes les commandes suivantes s’exécuteront dans `/app`.
- `COPY app.py /app`  : Copie le fichier `app.py` depuis la machine hôte vers le dossier `/app` du conteneur.  Cela intègre le code de l’application dans l’image.
- `RUN pip install streamlit`  : Exécute une commande pendant la construction de l’image.  Ici, on installe Streamlit dans l’environnement du conteneur.
- `EXPOSE 8080` : Indique que l’application utilise le port 8080.  Cela ne publie pas le port, mais documente l’usage pour Docker et les utilisateurs.
- `CMD ["streamlit", "run", "app.py", "--server.port=8080", "--server.address=0.0.0.0"]`  : Définit la commande exécutée automatiquement lorsque le conteneur démarre.  Ici, le conteneur lancera Streamlit et rendra l’application accessible depuis l’extérieur.


### Construire l’image Docker

Pour transformer un Dockerfile en une image exécutable, Docker utilise la commande `docker build`.  
Cette étape lit les instructions du Dockerfile et assemble une image prête à être lancée sous forme de conteneur.
La commande `docker build` sert à :

- lire le Dockerfile présent dans le dossier courant  
- télécharger les images de base nécessaires  
- copier les fichiers dans l’image  
- installer les dépendances  
- préparer l’environnement d’exécution  
- produire une image finale que l’on peut lancer avec `docker run`

En résumé, elle **compile** l’image à partir du Dockerfile.

```bash
docker build -t my-streamlit-app .
```

- `docker build` : lance la procédure de construction de l’image  
- `-t my-streamlit-app` : donne un nom (tag) à l’image pour pouvoir l’utiliser plus facilement  
- `.` : indique que le Dockerfile se trouve dans le répertoire courant

Après l’exécution, Docker crée une image appelée `my-streamlit-app`.

:warning: Le build d’une image Docker peut prendre plusieurs minutes, surtout si elle télécharge des dépendances ou des images de base, et l’image résultante peut être assez lourde.

Vérifier que l'image existe : 

```bash
docker images
```

### Lancer le conteneur

```bash
docker run -d -p 8080:8080 my-streamlit-app
```

- `-d` → détaché, le conteneur tourne en arrière-plan  
- `-p 8080:8080` → mappe le port 8080 du conteneur sur le port 8080 de la VM  

En local, l'application est accessible sur `http://localhost:8080/`.
Dans Play With Docker, cliquez sur le bouton **Open PORT** et tappez **8080** pour accéder à votre application Streamlit.

### En résumé

- Le Dockerfile sert à **décrire la recette complète** pour créer une image.  
- Il garantit que l’environnement est **identique partout**, quel que soit l’ordinateur ou le serveur.  
- Une fois le Dockerfile créé, on construit l’image avec :  
  `docker build -t my-streamlit-app .`  
- Puis on lance le conteneur avec :  
  `docker run -p 8080:8080 my-streamlit-app`

### Supprimer le conteneur

Docker permet de créer et gérer des conteneurs, et parfois il est nécessaire de les supprimer. Voici les principales méthodes.

Lister les conteneurs en cours d'exécution  : 

```bash
docker ps
```

- Si le conteneur n’est pas en cours d’exécution, il suffit de faire :
```bash
docker rm <container_name_or_id>
```
- Sinon : 
  - Pour un conteneur actif, il faut d’abord l’arrêter, puis le supprimer :
```bash
docker stop <container_name_or_id>
docker rm <container_name_or_id>
```

On peut aussi forcer la suppression (même si le conteneur tourne)

:warning: Utiliser avec précaution car cela arrête et supprime le conteneur immédiatement :
```bash
docker rm -f <container_name_or_id>
```

### Tableau récapitulatif des commandes

| Commande | Description |
|----------|-------------|
| docker build -t my-streamlit-app . | Construire l’image Docker |
| docker run -d -p 8080:8080 my-streamlit-app | Lancer le conteneur |
| docker ps | Lister les conteneurs actifs |
| docker ps -a | Lister tous les conteneurs |
| docker logs <id> | Afficher les logs d’un conteneur |
| docker stop <id> | Stopper un conteneur |
| docker rm <id> | Supprimer un conteneur |
| docker container prune | Supprimer tous les conteneurs arrêtés |

### 💡 Astuces

- Chaque modification de **app.py** nécessite de **rebuilder l’image** avec `docker build`  
- Vous pouvez créer plusieurs conteneurs de la même image sur différents ports pour tester plusieurs instances  
- Cette configuration est **légère** et adaptée à une VM avec **4 Go RAM**

#  Quelques notions de Réseau

## La notion de port sur un PC :

Imagine que ton **ordinateur est comme une maison** :

- L’ordinateur a une **seule adresse** (c’est l’IP) : c’est l’équivalent de l’adresse de ta maison dans la ville.  
- Mais dans cette maison, il y a **plein de portes** : la porte de la cuisine, la porte du salon, la porte de la chambre… chacune a une fonction spécifique.  

Chaque **port** sur l’ordinateur, c’est comme une **porte spéciale de la maison** :  

- Si quelqu’un veut t’envoyer un message pour regarder la télé, il va frapper à la **porte du salon** (port 80 pour un site web, par exemple).  
- Si quelqu’un veut t’envoyer un message pour le courrier électronique, il va frapper à la **porte de la salle de courrier** (port 25 pour le mail).  
- Si tu ne veux pas qu’on entre dans une pièce, tu peux **verrouiller la porte** (le port fermé).  

**Donc, un port, c’est juste une porte d’entrée virtuelle dans ton ordinateur** qui permet à des programmes différents de recevoir des messages différents, même si tout passe par la même adresse de la maison.

## Les ports sur le web et en localhost

- **Localhost (127.0.0.1)** = parler à soi-même dans sa maison.  
- Même en restant chez soi, chaque programme écoute sur **une porte spécifique** (un port) :  
  - Exemple : ton serveur web local écoute sur le **port 3000**.  
  - Pour y accéder : `http://localhost:3000` → tu ouvres la **porte 3000 de ta maison** pour voir ce qui se passe dans cette pièce.  

### En gros

| Concept informatique | Métaphore de la maison |
|--------------------|----------------------|
| Adresse IP / localhost | Adresse de la maison |
| Port | Porte spécifique de la maison |

**Résumé :** même sur ton propre ordinateur, chaque programme a sa **porte** pour recevoir les messages, et tout reste organisé sans se mélanger.

### Schéma récapitulatif

Adresse de la maison : 127.0.0.1 (localhost)

````markdown
          [Maison = ton PC]
  ---------------------------------
  |        Salon (Port 80)       |  ← site web
  |        Cuisine (Port 25)     |  ← emails
  |        Bureau (Port 22)      |  ← accès à distance
  |        Chambre (Port 3000)   |  ← site local / dev
  ---------------------------------
````

Chaque pièce = un programme/service
Chaque porte = un port qui reçoit les messages
Localhost = toi qui parles à ta propre maison

# Gestion des volumes Docker

Les volumes Docker permettent de **stocker des données de manière persistante** hors des conteneurs.  
Sans volume, toutes les données créées dans un conteneur disparaissent dès que le conteneur est supprimé.

## Pourquoi utiliser des volumes ?

- Préserver les données au-delà du cycle de vie d’un conteneur
- Partager des fichiers entre plusieurs conteneurs
- Séparer le stockage des données et l’image du conteneur
- Faciliter les sauvegardes et migrations

## Types de stockage

### Volumes nommés
- Créés et gérés par Docker
- Stockés dans `/var/lib/docker/volumes/`
- Exemple : `mysql_data` pour une base MySQL

### Bind mounts (montage de répertoire hôte)
- Lie un dossier du système hôte à un conteneur
- Utile pour le développement et le partage de fichiers
- Exemple : `-v /home/user/app:/app` dans un `docker run`


## Commandes utiles

### Créer un volume

```bash
docker volume create nom_du_volume
```

### Lister les volumes

```bash
docker volume ls
```

### Inspecter un volume

```bash
docker volume inspect nom_du_volume
```

### Supprimer un volume

```bash
docker volume rm nom_du_volume
```

:warning: supprimer un volume supprime **toutes les données stockées**.

## Gestion des volumes Docker avec des fichiers CSV

Les volumes Docker permettent de **stocker des fichiers de manière persistante**, même si le conteneur est supprimé.  
Pour illustrer cela, on va utiliser un conteneur Python qui génère des fichiers CSV dans un volume.

### Arborescence du projet

Créer un dossier de projet contenant :

````markdown
projet-volume/  
- generate_csv.py  
- Dockerfile  
````

Dans cet exemple, on utilise un Dockerfile pour créer une image Python contenant un script `generate_csv.py`.  
Le conteneur écrit un fichier CSV dans un **volume Docker**, ce qui permet de **persister les données** même après la suppression du conteneur.

### Préparer un script Python

Créer un fichier `generate_csv.py` :

```python
import csv  
import os  

os.makedirs("/data", exist_ok=True)  

with open("/data/contacts.csv", mode="w", newline="") as f:  
    writer = csv.writer(f)  
    writer.writerow(["Nom", "Email"])  
    writer.writerow(["Alice", "alice@example.com"])  
    writer.writerow(["Bob", "bob@example.com"])  

print("CSV généré dans /data/contacts.csv")  
```

### Créer un fichier Dockerfile

```dockerfile
FROM python:3.11-slim
WORKDIR /data
COPY generate_csv.py .
```

Le `.` à la fin signifie : copier generate_csv.py dans le dossier courant du conteneur (/data grâce à WORKDIR).

### Créer et monter un volume Docker

On va utiliser un volume nommé `csv_data` :

```shell
cd projet-volume
docker volume create csv_data
```

### Construire l’image Docker

```shell
docker build -t csv-generator .
```

### Lancer le conteneur pour générer le CSV

```shell
docker run --rm -v csv_data:/data csv-generator python generate_csv.py
```

- `-v csv_data:/data` → monte le volume pour stocker le CSV  
- `--rm` → supprime le conteneur après exécution  
- Le script écrit le fichier `contacts.csv` dans le volume
- 

### Vérifier la persistance

Lancer un nouveau conteneur pour lire le fichier CSV généré :

```shell
docker run --rm -v csv_data:/data -w /data python:3.11-slim cat contacts.csv
```

- Tu verras le contenu du CSV même si le conteneur qui l’a créé a été supprimé  
- Cela démontre la **persistance des données avec les volumes Docker**

### Lister les fichiers dans un volume nommé

```shell
docker run --rm -v csv_data:/data -w /data busybox ls -l
```

- `--rm` : supprime le conteneur après exécution  
- `-v csv_data:/data` : monte le volume dans `/data` du conteneur  
- `-w /data` : définit le dossier courant du conteneur  
- `busybox` : image légère pour exécuter des commandes Unix  
- `ls -l` : liste les fichiers avec détails

### Explorer le volume en mode interactif

```shell
docker run --rm -it -v csv_data:/data -w /data busybox sh
```

- `-it` : mode interactif + terminal  
- `sh` : ouvre un shell dans le conteneur

Une fois à l’intérieur, tu peux utiliser :

- `ls` : lister les fichiers  
- `ls -l` : lister avec détails  
- `cat contacts.csv` : afficher le contenu d’un fichier  

Tape `exit` pour quitter le conteneur.

### Bonnes pratiques

- Utiliser des volumes pour toutes les données importantes (CSV, bases, logs…)  
- Nommer les volumes de manière explicite pour éviter les confusions (`csv_data`, `mysql_data`)  
- Supprimer les volumes inutilisés avec `docker volume prune`  

## Générer un CSV avec Docker et monter un dossier local

Cette méthode utilise un **bind mount** pour stocker les fichiers CSV directement sur le système de fichiers de l'hôte.

### Modifier le script `generate_csv.py`

```python
import csv  
import os  

# Créer le dossier de sortie dans le conteneur
os.makedirs("/data/output", exist_ok=True)

with open("/data/output/contacts.csv", mode="w", newline="") as f:  
    writer = csv.writer(f)  
    writer.writerow(["Nom", "Email"])  
    writer.writerow(["Alice", "alice@example.com"])  
    writer.writerow(["Bob", "bob@example.com"])

print("CSV généré dans /data/output/contacts.csv")
```

### Préparer un dossier local pour stocker les CSV

Par exemple, créer un dossier `csv_output` dans `projet-volume/` :

```shell
mkdir csv_output
```

### Construire l’image Docker


```shell
docker build -t csv-generator-local .
```

### Lancer le conteneur Python avec un bind mount

Pour Linux : 

```shell
docker run --rm -v $(pwd)/csv_output:/data csv-generator-local python generate_csv.py
```

Pour Windows PowerShell: 

```shell
docker run --rm -v ${PWD}/csv_output:/data/output csv-generator-local python generate_csv.py
```

- `-v $(pwd)/csv_output:/data` : lie le dossier local `csv_output` au dossier `/data` dans le conteneur  
- `-w /data` : définit le dossier courant dans le conteneur  
- Le script écrit `contacts.csv` dans `/data` → apparaît automatiquement dans `csv_output` sur l’hôte

### Vérifier le fichier sur l’hôte

```shell
ls csv_output
```

- Le fichier `contacts.csv`est généré  
- Le fichier est persistant et accessible directement depuis ton système de fichiers


# Introduction à Docker Compose

## Objectif

- Lancer MySQL et Streamlit avec Docker Compose  
- Connecter Streamlit à la base MySQL  
- Insérer des données via un formulaire web  
- Tester l’ensemble en local, sans Docker Swarm  

Docker Compose permet de définir et lancer plusieurs services à partir d'un seul fichier `docker-compose.yml`.  
Chaque service représente un conteneur (ex : MySQL, une application web, un backend…).  
Compose gère automatiquement le réseau, les volumes et l'ordre de démarrage.

Dans notre projet, le fichier `docker-compose.yml` contient deux services :

- `mysql-db` : la base de données MySQL  
- `streamlit-app` : l'application web

Compose crée aussi un réseau interne pour que les conteneurs puissent communiquer entre eux en utilisant leurs noms de service comme hostname.

## Arborescence du projet

Créer un dossier de projet contenant :

````markdown
projet-streamlit-mysql/  
- app.py  
- requirements.txt  
- Dockerfile  
- docker-compose.yaml  
````

## Fichier app.py

```python
import streamlit as st  
import mysql.connector  

conn = mysql.connector.connect(  
    host="mysql-db",  
    user="user",  
    password="password",  
    database="contactsdb"  
)  
cursor = conn.cursor()  

st.title("Formulaire de contact")  
name = st.text_input("Nom")  
email = st.text_input("Email")  
message = st.text_area("Message")  

if st.button("Envoyer"):  
    if name and email and message:  
        cursor.execute(  
            "INSERT INTO contacts (name,email,message) VALUES (%s,%s,%s)",  
            (name, email, message)  
        )  
        conn.commit()  
        st.success("Message ajouté !")  
    else:  
        st.error("Remplissez tous les champs !")  
```

## Fichier requirements.txt

```txt
streamlit  
mysql-connector-python  
```


## Fichier Dockerfile

```dockerfile
FROM python:3.11-slim  
WORKDIR /app  
COPY requirements.txt ./  
RUN pip install --no-cache-dir -r requirements.txt  
COPY . .  
CMD ["streamlit","run","app.py","--server.port=8501","--server.address=0.0.0.0"]  
```

## Fichier docker-compose.yml

```yaml
version: "3.9"  

services:  

  mysql-db:  
    image: mysql:8.0  
    container_name: mysql-db  
    environment:  
      MYSQL_ROOT_PASSWORD: root  
      MYSQL_DATABASE: contactsdb  
      MYSQL_USER: user  
      MYSQL_PASSWORD: password  
    ports:  
      - "3306:3306"  
    volumes:  
      - mysql_data:/var/lib/mysql  
    networks:  
      - app-network  

  streamlit-app:  
    build: .  
    container_name: streamlit-app  
    depends_on:  
      - mysql-db  
    ports:  
      - "8501:8501"  
    networks:  
      - app-network  

networks:  
  app-network:  

volumes:  
  mysql_data:  
```

## Créer la table MySQL

Avant de démarrer toute l'application, il peut être utile de lancer seulement le service MySQL, par exemple pour créer la base ou la table.
La commande suivante démarre uniquement le service `mysql-db` :

```shell
docker compose up mysql-db -d  
```

- `mysql-db` : nom du service à lancer  
- `-d` : exécution en mode détaché (le conteneur tourne en arrière-plan)

:bulb: Une fois MySQL lancé, il devient accessible depuis les autres services Docker via le hostname `mysql-db`, et depuis l’hôte en utilisant le port 3306 si celui-ci est exposé dans le fichier Compose.

Une fois le service MySQL lancé avec Docker Compose, il peut être utile d’ouvrir une session MySQL directement à l’intérieur du conteneur.  
Cela permet de vérifier l’état de la base, de créer des tables ou de consulter les données.

La commande suivante ouvre un terminal interactif à l’intérieur du conteneur `mysql-db`  
et lance le client MySQL connecté à la base `contactsdb` :

```shell
docker exec -it mysql-db mysql -h mysql-db -uuser -ppassword contactsdb
```

- `docker exec` : exécute une commande dans un conteneur en cours d’exécution  
- `-it` : mode interactif + terminal  
- `mysql-db` : nom du conteneur où exécuter la commande  
- `mysql` : lance le client MySQL installé dans le conteneur  
- `-h mysql-db` : indique l’hôte MySQL (le nom du service dans Docker Compose)  
- `-uuser` : nom d’utilisateur MySQL  
- `-ppassword` : mot de passe MySQL  
- `contactsdb` : base de données à utiliser

Créer la table :  

```sql
CREATE TABLE contacts (  
    id INT AUTO_INCREMENT PRIMARY KEY,  
    name VARCHAR(255),  
    email VARCHAR(255),  
    message TEXT  
);
```  

```shell
exit  
```

## Lancer toute l’application

Une fois que tous les fichiers du projet sont prêts (Dockerfile, app.py, requirements, docker-compose.yml), Docker Compose peut construire les images et démarrer l’ensemble des services.

```shell
docker compose up --build
```

Docker Compose :
- crée les conteneurs  
- met en place le réseau interne  
- attache les logs des services au terminal  
- démarre MySQL puis l’application Streamlit qui dépend de MySQL


Ouvrir Streamlit sur : http://localhost:8501  


## Vérifier les données

:warning: l'application MySQL est ouvert en intéractif + terminal c'est pourquoi il faut ouvrir un nouveau terminal pour la commande suivante : 

```shell
docker exec -it mysql-db mysql -h mysql-db -uuser -ppassword contactsdb
```

```sql
SELECT * FROM contacts;  
```

:bulb: Pourquoi `docker compose up --build` ne réinitialise pas MySQL ? Lorsque tu relances l’application avec `docker compose up --build`, MySQL ne repart pas de zéro.  La raison est simple : Docker Compose utilise un **volume persistant** pour stocker les données MySQL.

Dans le fichier `docker-compose.yml`, on trouve :

```yaml
- mysql_data:/var/lib/mysql
```

Ce volume `mysql_data` contient toutes les données MySQL : la base, les tables et leur contenu.  
Les volumes Docker sont conçus pour **survivre** aux redémarrages et aux reconstructions d’images.  
Ainsi, même si l’on reconstruit l’image ou recrée les conteneurs, MySQL retrouve automatiquement ses données.


## Nettoyer

```shell
docker compose down  
docker compose down -v
```

La commande `docker compose down -v` arrête les conteneurs, supprime le réseau et **efface les volumes**, ce qui vide totalement MySQL.  
Au prochain démarrage, MySQL sera comme neuf.


# Introduction à Docker Swarm Mode

## Découverte

Jusqu’ici, nous avons utilisé **Docker sur un seul hôte**, avec quelques conteneurs isolés.  
Mais en production, une application peut impliquer **des dizaines ou centaines de conteneurs** : base de données, front-end, API, workers, etc.  
Pour coordonner tout cela, il faut un outil d’orchestration.

Docker propose **deux outils principaux** :
- **Docker Compose** → pour gérer plusieurs conteneurs sur une même machine.
- **Docker Swarm Mode** → pour gérer plusieurs *machines Docker* en cluster, avec haute disponibilité et scalabilité.

## Docker Swarm Mode : qu’est-ce que c’est ?

**Docker Swarm** permet de :
- Déployer et coordonner plusieurs nœuds (machines Docker).
- Répartir automatiquement les conteneurs entre les nœuds.
- Assurer la **haute disponibilité** (HA) avec plusieurs *managers*.
- Offrir le **scaling** et le **load balancing** intégrés.

Un cluster Swarm contient :
- Des **nœuds managers** (gèrent le cluster, peuvent aussi exécuter des conteneurs).
- Des **nœuds workers** (exécutent les conteneurs selon les ordres des managers).

### Rôles des nœuds
- **Manager**
  - Responsable de la **gestion du cluster** : planification des services, gestion de l’état des nœuds et des tâches.  
  - Prend les décisions sur où et combien de containers déployer.  
  - Peut aussi exécuter des containers, mais son rôle principal est administratif.  
- **Worker**
  - Exécute les **containers assignés** par le manager.  
  - Ne prend pas de décision sur la planification.  
  
### Intérêt
- Permet de **scaler facilement** les services (augmenter/diminuer les réplicas).  
- Assure la **haute disponibilité** : si un nœud worker tombe, le manager redéploie les containers sur d’autres nœuds.  
- Le manager peut avoir **plusieurs instances** pour garantir la tolérance aux pannes. 
  
Schéma mental : le manager est le "chef d’orchestre", les workers sont les musiciens qui exécutent les tâches.

## Déploiement et Scaling d’un Service Web

### Objectif

Ce tutoriel montre comment :

- Créer un cluster Swarm (plusieurs nœuds Docker qui coopèrent)  
- Déployer un service web distribué (Nginx)  
- Scaler ce service pour gérer plus de charge  
- Comprendre comment Docker Swarm assure la répartition de charge et la tolérance aux pannes  

### Lancer l’environnement

:warning: Pour simplifier ce cas pratique, *utilisez Play With Docker* plutôt que votre machine local, cela permettra de créer des clusters plus facilement et rapidement.

1. Va sur [Play With Docker](https://labs.play-with-docker.com/).  
2. Clique sur **Start**, puis **Add New Instance** → tu obtiens une VM Linux (`node1`).  
3. Ajoute deux autres instances avec **Add New Instance** → tu as maintenant 3 nœuds : `node1`, `node2`, `node3`.  

### Initialiser le Swarm

Sur le nœud manager (**node1**) :

```bash
docker swarm init --advertise-addr $(hostname -i)
```

Docker crée le cluster Swarm et t’affiche une commande `docker swarm join` à exécuter sur les autres nœuds.

Sur les autres nœuds (**node2, node3, …**) :

```bash
docker swarm join --token <token> <IP_manager>:2377)
```

### Vérifier les nœuds du cluster

Sur le manager :

```bash
docker node ls
```

Exemple de sortie :

````markdown
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS  
kytp4gq5mrvmdbb0qpifdxeiv *  node1     Ready   Active        Leader  
lz1j4d6290j8lityk4w0cxls5    node2     Ready   Active  
qpl9d95hd8z0l1aox6uwb90d7    node3     Ready   Active  
````

Le symbole * indique le manager.  
Seul le leader peut exécuter des commandes administratives comme `docker node ls`.

### Créer un service web

Créons un service **Nginx** exposé sur le port 80 :

```bash
docker service create -p 80:80 --name web nginx:latest
```

Puis vérifie qu’il est bien en cours d’exécution :

```bash
docker service ls
```

Exemple :

````markdown
ID             NAME      MODE         REPLICAS   IMAGE           PORTS  
9h8fz9rhv7uk   web       replicated   1/1        nginx:latest    *:80->80/tcp
````

### Tester le service

Sur n’importe quel nœud du cluster :

```bash
curl http://localhost:80
```

Tu devrais voir la page par défaut de **Nginx**.

### Scaler (augmenter le nombre d’instances)

Pour ajouter plus d’instances du service :

```bash
docker service scale web=15
```

Docker va automatiquement répartir les 15 containers Nginx sur les différents nœuds disponibles.

Vérifie leur répartition :

```bash
docker service ps web
```

Exemple :

````markdown
ID             NAME      IMAGE           NODE      DESIRED STATE   CURRENT STATE  
t5h2x3l7j9z0   web.1     nginx:latest    node1     Running         Running 1m ago  
hj2a7c6g2s8l   web.2     nginx:latest    node2     Running         Running 1m ago  
f9q3d8h4k2a7   web.3     nginx:latest    node3     Running         Running 1m ago  
...
````

### Load Balancing Automatique

Docker Swarm gère automatiquement la répartition du trafic entre tous les containers du service.  
Même si ton service expose le port 80 sur un seul nœud, Swarm redirige les connexions vers n’importe quelle instance disponible.

Cela signifie que tous les containers web participent à servir les utilisateurs, même s’ils sont sur différents nœuds.

### Tolérance aux pannes

Si un nœud tombe, Docker redéploie automatiquement ses containers sur d’autres nœuds :

```bash
docker node update --availability drain node2
```

Cela simule la mise hors service de node2.  

Vérifie ensuite :

```bash
docker service ps web
```

Les containers qui étaient sur node2 ont été automatiquement replacés sur node1 et node3.

Pour le remettre en ligne :

```bash
docker node update --availability active node2
docker service ps web
```


### Réduire le nombre d’instances

Pour réduire la charge :

```bash
docker service scale web=10
docker service ps web
```

Swarm va supprimer 5 containers (au hasard), tout en gardant le service fonctionnel.

### Compréhension du fonctionnement

#### Swarm = un cluster de serveurs Docker

Swarm transforme plusieurs hôtes Docker en un seul cluster logique.  
Le manager orchestre les déploiements, les workers hébergent les containers.

#### Service = application distribuée

Un “service” est une application déclarée (ex: Nginx).  
Swarm la déploie sous forme de réplicas (plusieurs containers identiques).

#### Scaling = augmenter la capacité

```bash
docker service scale web=15
docker service ps web
```

signifie : "Lance 15 serveurs Nginx identiques répartis dans le cluster."

Plus de réplicas = plus de puissance pour servir des utilisateurs simultanés.

#### Load Balancing intégré

Swarm agit comme un répartiteur de charge interne :  
Le port 80 du cluster redirige les connexions vers n’importe quel replica.

Cela équilibre automatiquement le trafic entre les containers.  
Aucun proxy manuel n’est nécessaire.

#### Résilience

Swarm surveille l’état des containers.  
S’il détecte une panne, il redéploie les instances ailleurs.  
C’est le principe de la **haute disponibilité (HA)**.

#### Supprimer le service

Pour supprimer le service **web** :

```bash
docker service rm web
```

Vérifie ensuite qu’il a bien été supprimé :

```bash
docker service ls
```

### En résumé

| Fonction | Commande | Explication |
|-----------|-----------|-------------|
| Créer le cluster | docker swarm init | Démarre Swarm et définit le manager |
| Joindre un nœud | docker swarm join | Ajoute un worker au cluster |
| Lister les nœuds | docker node ls | Vérifie les membres du cluster |
| Déployer un service | docker service create | Lance une app dans le cluster |
| Scaler le service | docker service scale web=15 | Multiplie le nombre d’instances |
| Répartir la charge | (automatique) | Load balancing intégré |
| Gérer les pannes | docker node update --availability drain | Déplace les services en cas de problème |


### Exemple visuel

```
          ┌────────────┐
          │  Manager   │
          │  node1     │
          └─────┬──────┘
                │
 ┌──────────────┼────────────────┐
 │              │                │
▼              ▼                ▼
node1         node2            node3
(web.1)       (web.2)          (web.3)
(web.4)       (web.5)          (web.6)
   │             │                │
   └─────── Load Balancer ────────┘
                │
          Utilisateurs 🌍
```


### À retenir

- Chaque container = une instance serveur, pas un utilisateur.  
- Les utilisateurs se connectent à travers le port exposé, et Swarm distribue leurs requêtes.  
- Tu peux donc simuler plus de serveurs web (scaling), pas plus d’utilisateurs.  
- Swarm rend ton application plus robuste, scalable et hautement disponible.  


## Formulaire Web Streamlit + MySQL  avec Play With Docker

### Objectif

- Créer un cluster Swarm (1 manager + 2 workers) sur PWD  
- Déployer un service MySQL pour stocker les données  
- Déployer un service Streamlit avec un formulaire web  
- Insérer les infos du formulaire dans la base MySQL  
- Tester la communication entre les containers et observer le fonctionnement du Swarm  

### Préparer l’environnement PWD

Crée 3 instances dans PWD :  

- node1 → manager  
- node2 → worker  
- node3 → worker  

Sur **node1 (manager)**, initialise le Swarm :

```bash
docker swarm init --advertise-addr $(hostname -i)
```

Copie la commande `docker swarm join` affichée par Docker.  

Sur **node2** et **node3 (workers)**, colle la commande join pour les joindre au Swarm.  

Vérifie sur **node1** que tous les nœuds sont prêts :

```bash
docker node ls
```

### Créer un réseau overlay

Sur **node1 (manager)** :

```bash
docker network create --driver overlay app-network
```

Tous les services doivent être sur ce réseau pour communiquer entre eux.

### Déployer MySQL

Sur **node1** :

```bash
docker service create --name mysql-db --network app-network --env MYSQL_ROOT_PASSWORD=root --env MYSQL_DATABASE=contactsdb --env MYSQL_USER=user --env MYSQL_PASSWORD=password mysql:8.0
```

- Nom du service : **mysql-db**  
- Base : **contactsdb**  
- Utilisateur : **user / password**  

Tester MySQL :  

Récupère le container MySQL :

```bash
docker ps
```

Connecte-toi à MySQL :  

```bash
docker exec -it <container_id> mysql -uuser -ppassword contactsdb
```

Crée la table `contacts` :

```bash
CREATE TABLE contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    message TEXT
);
```

puis tappez `exit`.

### Préparer l’application Streamlit

Sur **node1**, crée un dossier `app` et les fichiers nécessaires depuis le terminal : 

```bash
mkdir app
cd app
```

#### Créer `app.py` 

Créer le fichier avec la commande `touch app.py` puis dans l'editeur de fichier PWD copier coller le script ci-dessous.

```python
import streamlit as st
import mysql.connector

conn = mysql.connector.connect(host="mysql-db", user="user", password="password", database="contactsdb")
cursor = conn.cursor()

st.title("Formulaire de contact")
name = st.text_input("Nom")
email = st.text_input("Email")
message = st.text_area("Message")

if st.button("Envoyer"):
    if name and email and message:
        cursor.execute("INSERT INTO contacts (name,email,message) VALUES (%s,%s,%s)", (name,email,message))
        conn.commit()
        st.success("Message ajouté !")
    else:
        st.error("Remplissez tous les champs !")
```


#### Créer `requirements.txt` 

Créer le fichier avec la commande `touch requirements.txt` puis dans l'editeur de fichier PWD copier coller le script ci-dessous.

```txt
streamlit
mysql-connector-python
```

#### Créer `Dockerfile`

Créer le fichier avec la commande `touch Dockerfile` puis dans l'editeur de fichier PWD copier coller le script ci-dessous.

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["streamlit","run","app.py","--server.port=8501","--server.address=0.0.0.0"]
```

### Construire l’image Streamlit

Sur **node1** :

```bash
docker build -t streamlit-app .
```

### Déployer le service Streamlit sur le Swarm

Sur PWD, l’image est locale sur node1 seulement.  
Les autres nœuds n’ont pas l’image et ne peuvent pas démarrer le container.  
Pour que ça fonctionne, on force le service à tourner sur **node1** :

```bash
docker service create --name streamlit-app --network app-network --replicas 1 -p 8501:8501 --constraint 'node.hostname==node1' streamlit-app
```

- `--constraint 'node.hostname==node1'` → force le container sur node1  
- `-p 8501:8501` → expose Streamlit pour le navigateur  

### Tester l’application

Depuis le navigateur PWD : ouvrir le port `8501`

- Remplir le formulaire et clique sur “Envoyer”  
- Vérifir que les données ont été ajoutées dans MySQL :

```bash
docker exec -it $(docker ps -q -f name=mysql-db) mysql -uuser -ppassword contactsdb
```

```bash
SELECT * FROM contacts;
```

Tu dois voir le nom, l’email et le message ajoutés depuis Streamlit.

### Nettoyer

```bash
docker service rm streamlit-app  
```

```bash
docker service rm mysql-db  
```

```bash
docker network rm app-network
```

### Conseils PWD

- Exécuter toutes les commandes Swarm sur le **manager (node1)**  
- Les services doivent être sur le même **réseau overlay**  
- Les volumes MySQL sont temporaires sur PWD  
- Streamlit peut être scalé, mais avec image locale sur PWD, il faut forcer le nœud ou pousser sur Docker Hub  

### Schéma visuel du TP

```
          ┌────────────┐
          │  Manager   │
          │  node1     │
          └─────┬──────┘
               │
       ┌───────┴────────┐
       │                │
      node2             node3
     (worker)          (worker)
       │                │
       └────────────┬───┘
                    │
           ┌────────┴─────────┐
           │    Overlay       │
           │   app-network    │
           └────────┬─────────┘
                    │
        ┌───────────┴───────────┐
        │                       │
   ┌─────────────┐        ┌──────────────┐
   │  MySQL DB   │        │ Streamlit App│
   │ mysql-db    │        │ streamlit-app│
   └─────────────┘        └──────────────┘
```


# Mini-projet Docker : Extraction des données DPE par département

## Objectif pédagogique

Ce mini-projet a pour but de mettre en pratique les compétences Docker et Python acquises dans le TP précédent.  
Les étudiants devront :  

- Interroger l’API DPE de l’ADEME pour plusieurs départements  
- Stocker les résultats dans des fichiers Parquet  
- Exécuter un conteneur par département pour isoler le traitement  
- Utiliser un volume local pour persister les fichiers sur l’hôte  

Ce projet combine **Docker, Python, volumes/bind mounts, et paramétrisation par département**.

## Consignes générales

1. **Paramétrisation par département**  
   - Fournir une liste de départements à traiter (ex. : 75, 69, 13)  
   - Chaque conteneur doit récupérer le département via une variable d’environnement ou un paramètre  

2. **Isolation avec Docker**  
   - Construire une image Docker contenant le script Python qui interroge l’API et écrit un fichier Parquet  
   - Lancer un conteneur **par département**, pour générer le fichier correspondant  

3. **Persistance des fichiers**  
   - Créer un dossier local sur l’hôte (ex. `dpe_output`)  
   - Monter ce dossier dans chaque conteneur pour stocker les fichiers Parquet  
   - Vérifier que les fichiers générés sont accessibles après suppression des conteneurs  

4. **Organisation et bonnes pratiques**  
   - Le script Python doit créer le dossier de sortie dans le conteneur si nécessaire  
   - Rebuild de l’image Docker nécessaire si le script ou Dockerfile est modifié  
   - Ne pas monter le dossier `/data` de l’image directement pour ne pas écraser le script  
   - Monter un **sous-dossier** pour la sortie afin d’éviter les conflits  

5. **Exécution parallèle**  
   - Les conteneurs peuvent être exécutés en parallèle
   - Chaque conteneur doit produire un fichier Parquet distinct correspondant au département traité  


## Livrables attendus

- Un **Dockerfile** fonctionnel contenant le script Python  
- Un **script Python** paramétré pour interroger l’API DPE selon le département  
- Un **dossier local** rempli de fichiers Parquet (`dpe_<departement>.parquet`)  
- Une courte documentation expliquant :  
  - Comment exécuter les conteneurs  
  - Comment vérifier la présence et le contenu des fichiers  
  - Comment ajouter de nouveaux départements à traiter  

## Points pédagogiques visés

- Compréhension des **conteneurs Docker et des volumes/bind mounts**  
- Paramétrisation et réutilisation d’images Docker pour plusieurs cas  
- Gestion de données externes (API) et persistance sur l’hôte  
- Organisation de traitements indépendants pour plusieurs départements  
- Workflow complet **développement → build → exécution → persistance**

# Docker 🐳

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
- Si elle n’existe pas, elle est téléchargée depuis Docker Hub.  
- Le conteneur s’exécute, affiche un message, puis se ferme automatiquement.

### Télécharger une image légère

Téléchargez l’image Alpine Linux (très légère) :

```bash
docker image pull alpine
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

### Créer l’application Streamlit

1. Dans le terminal, créez un dossier pour l’application :  

```bash
mkdir streamlit_app && cd streamlit_app
```

2. Créez le fichier **app.py** :  

```bash
echo "import streamlit as st
st.title('Hello Docker!')
st.write('Ceci est une app Streamlit dans un conteneur Docker 🐳')
st.line_chart({'data': [1, 3, 2, 4, 5, 3]})
" > app.py
```


### Créer le Dockerfile

Dans le même dossier, créez le fichier **Dockerfile** :  

```bash
echo "FROM python:3.11-slim
WORKDIR /app
COPY app.py /app
RUN pip install streamlit
EXPOSE 8080
CMD [\"streamlit\", \"run\", \"app.py\", \"--server.port=8080\", \"--server.address=0.0.0.0\"]" > Dockerfile
```


### Construire l’image Docker

```bash
docker build -t my-streamlit-app .
```

- `-t my-streamlit-app` → nom de l’image  
- Le build télécharge Python, installe Streamlit et prépare l’image

### Lancer le conteneur

```bash
docker run -d -p 8080:8080 my-streamlit-app
```

- `-d` → détaché, le conteneur tourne en arrière-plan  
- `-p 8080:8080` → mappe le port 8080 du conteneur sur le port 8080 de la VM  

Dans Play With Docker, cliquez sur le bouton **Open PORT** et tappez **8080** pour accéder à votre application Streamlit.

### Supprimer le conteneur

Lister les conteneurs en cours d'exécution  : 

```bash
docker ps
```

Supprimer un conteneur : 

```bash
docker rm <container_id>
#ou
docker rm <container_name>
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


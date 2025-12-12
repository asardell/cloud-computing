# KAFKA

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/Apache_kafka_wordtype.svg/2560px-Apache_kafka_wordtype.svg.png" alt="Source de l'image" width="600"/>
</p>

- [KAFKA](#kafka)
- [Introduction à Apache Kafka](#introduction-à-apache-kafka)
  - [Pourquoi Kafka en entreprise ?](#pourquoi-kafka-en-entreprise-)
  - [Exemples de cas d’usage en entreprise](#exemples-de-cas-dusage-en-entreprise)
  - [Avantages](#avantages)
  - [Inconvénients](#inconvénients)
  - [Images Docker utilisées](#images-docker-utilisées)
  - [Kafka](#kafka-1)
  - [Kafdrop](#kafdrop)
  - [Pourquoi pas Kafka officiel ?](#pourquoi-pas-kafka-officiel-)
- [Exercices](#exercices)
  - [Exercice 1 : Envoyer des messages vers Kafka avec Docker](#exercice-1--envoyer-des-messages-vers-kafka-avec-docker)
    - [Objectif de l’exercice](#objectif-de-lexercice)
    - [Arborescence](#arborescence)
    - [Commandes Docker](#commandes-docker)
  - [Exercice 2 : Envoyer des données depuis une API vers Kafka](#exercice-2--envoyer-des-données-depuis-une-api-vers-kafka)
    - [Objectif de l’exercice](#objectif-de-lexercice-1)
    - [Arborescence](#arborescence-1)
    - [Commandes Docker](#commandes-docker-1)
  - [Exercice 3 : Pipeline complet Kafka avec Producer et Consumer](#exercice-3--pipeline-complet-kafka-avec-producer-et-consumer)
    - [Objectif de l’exercice](#objectif-de-lexercice-2)
    - [Arborescence](#arborescence-2)
    - [Commandes Docker](#commandes-docker-2)
  - [Exercice 4 : Kafka → PostgreSQL avec Producer et Consumer](#exercice-4--kafka--postgresql-avec-producer-et-consumer)
    - [Objectif de l’exercice](#objectif-de-lexercice-3)
    - [Arborescence](#arborescence-3)
    - [Commandes Docker](#commandes-docker-3)
  - [Exercice 5 : Kafka avec plusieurs partitions et scaling des consumers](#exercice-5--kafka-avec-plusieurs-partitions-et-scaling-des-consumers)
    - [Objectif de l’exercice](#objectif-de-lexercice-4)
    - [Arborescence](#arborescence-4)
    - [Commandes Docker](#commandes-docker-4)
  - [Exercice 6 : Kafka avec consommateurs spécialisés (DB + Analytics)](#exercice-6--kafka-avec-consommateurs-spécialisés-db--analytics)
    - [Objectif de l’exercice](#objectif-de-lexercice-5)
    - [Arborescence](#arborescence-5)
    - [Commandes Docker](#commandes-docker-5)
  - [Mots-clés et notions essentielles du TP Kafka avec Docker](#mots-clés-et-notions-essentielles-du-tp-kafka-avec-docker)
  - [Aller plus loin avec Kafka](#aller-plus-loin-avec-kafka)
- [Mini-projet Kafka : Ingestion des données DPE par département](#mini-projet-kafka--ingestion-des-données-dpe-par-département)
  - [Objectif pédagogique](#objectif-pédagogique)
  - [Consignes générales](#consignes-générales)
  - [Points visés](#points-visés)

# Introduction à Apache Kafka

**Objectif :** Découvrir Kafka, ses concepts clés, comprendre pourquoi cette technologie est utilisée en entreprise pour gérer des flux de données en temps réel.

Kafka est utilisé dans l’industrie pour absorber de grands flux de données.  
Ici, nous allons le manipuler dans un environnement simplifié mais réaliste, orchestré par Docker.

## Pourquoi Kafka en entreprise ?

- **Ingestion massive de données** : logs, IoT, applications web, pipelines ETL  
- **Traitement en temps réel** : alertes, analyses, transformations instantanées  
- **Communication entre microservices** : découplage producteur / consommateur  
- **Persistance et fiabilité** : messages stockés durablement, tolérance aux pannes  

## Exemples de cas d’usage en entreprise

- **E-commerce** : suivi des clics et interactions des utilisateurs pour personnaliser l’expérience ou déclencher des recommandations en temps réel.
- **Banque / Finance** : détection de fraude en temps réel en analysant les transactions au fur et à mesure.
- **IoT / Industrie** : collecte massive de données issues de capteurs pour la maintenance prédictive.
- **Monitoring et logs** : centralisation des logs d’applications distribuées pour l’analyse et la détection d’anomalies.
- **Streaming multimédia** : traitement et distribution de flux vidéo ou audio en temps réel.

## Avantages

| Avantage | Explication |
|----------|-------------|
| Haute performance | Capable de traiter des millions de messages par seconde |
| Scalabilité | Ajout facile de brokers et de partitions pour gérer le volume |
| Durabilité | Messages persistants sur disque |
| Tolérance aux pannes | Réplication des partitions entre brokers |
| Flexibilité | Intégration facile avec Python, Spark, Flink, etc. |

## Inconvénients

| Inconvénient | Explication |
|--------------|-------------|
| Courbe d’apprentissage | Concepts nouveaux comme offset, partitions, consumer groups |
| Administration technique | Gestion d’un cluster distribué, monitoring, réplication |
| Complexité pour petits projets | Peut être surdimensionné pour des flux simples |
| Ordre des messages partiel | L’ordre global n’est pas garanti entre plusieurs partitions |


## Images Docker utilisées

Pour manipuler Kafka et visualiser vos messages, nous utilisons deux images Docker principales.  
:warning: ces images ne sont **pas les images officielles de Kafka**, mais des versions simplifiées et pédagogiques. Elles sont légères et faciles à démarrer, mais certaines fonctionnalités avancées de Kafka ne sont pas disponibles (par exemple, la configuration fine du cluster, la sécurité avancée, etc.)

## Kafka

```yaml
kafka:
  image: obsidiandynamics/kafka
```

- **Rôle** : C’est le broker Kafka qui reçoit, stocke et distribue les messages.
- **Fonctionnement** :
  - Chaque message envoyé par un producteur est stocké dans un **topic**.
  - Les messages sont organisés en **partitions** pour permettre la scalabilité.
  - Les consommateurs lisent les messages de ces topics à leur rythme.
- **Pourquoi cette image** : `obsidiandynamics/kafka` est une image simple, prête à l’emploi et adaptée à un environnement pédagogique. Elle inclut tout le nécessaire pour démarrer un broker Kafka rapidement avec Docker, sans nécessiter une configuration complexe.

## Kafdrop

```yaml
kafdrop:
  image: obsidiandynamics/kafdrop
```

- **Rôle** : Interface web pour explorer les topics Kafka et visualiser les messages.
- **Fonctionnalités principales** :
  - Lister tous les topics présents dans le broker.
  - Inspecter les messages d’un topic en temps réel.
  - Voir les **consumer groups** et leur progression (offsets).
- **Pourquoi cette image** : Elle permet de visualiser facilement ce qui se passe dans Kafka, sans avoir besoin de commandes en ligne complexes. C’est très pratique pour comprendre le flux producteur → broker → consommateur.

:bulb:**Astuce pédagogique** : Avec ces images légères, on peut **envoyer des messages avec Python** et les **observer dans Kafdrop**, ce qui rend les concepts abstraits beaucoup plus concrets, même si certaines fonctionnalités avancées de Kafka ne sont pas accessibles dans cet environnement.

## Pourquoi pas Kafka officiel ?

L'image officielle de Kafka (disponible sous  `apache/kafka`) est plus complète et correspond à un environnement de production.

- **Rôle principal** : Même rôle que l’image pédagogique : broker Kafka qui stocke et distribue les messages.  
- **Zookeeper** : Kafka utilise Zookeeper pour gérer le **cluster**, les **leader/follower des partitions** et la **coordination des brokers**. Dans les versions récentes, Kafka peut fonctionner sans Zookeeper (KRaft mode), mais beaucoup de configurations classiques utilisent encore Zookeeper.
- **Avantages de l’image officielle** :
  - Accès à toutes les fonctionnalités de Kafka (transactions, ACL, configurations avancées)
  - Compatible avec un vrai cluster de production
  - Support officiel et mises à jour régulières
- **Inconvénients** :
  - Plus lourde et complexe à configurer
  - Nécessite souvent la mise en place d’un cluster Zookeeper séparé
  - Moins pratique pour un environnement pédagogique simple

:bulb:**Résumé pédagogique** : L’image officielle est idéale pour la production et l’apprentissage avancé, mais pour un cours ou des exercices simples, les images légères comme `obsidiandynamics/kafka` sont plus faciles à manipuler.

# Exercices

## Exercice 1 : Envoyer des messages vers Kafka avec Docker

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Mettre en place un **environnement Kafka simplifié** avec Docker, accompagné de Kafdrop pour visualiser les messages.  
- Créer un **producteur Python** qui envoie des messages à Kafka toutes les 5 secondes.  
- Comprendre le rôle de chaque service et la communication entre eux (Kafka ↔ Producer ↔ Kafdrop).  

Cet exercice permet de **manipuler concrètement Kafka**, voir les messages en temps réel via Kafdrop et comprendre le flux producteur → broker → consommateur.

### Arborescence

```txt
kafka-exo1  
 ├── docker-compose.yml  
 ├── producer/  
 │    ├── producer.py  
 │    └── Dockerfile  
```

1) `docker-compose.yml`

```yaml
services:  
  kafka:  
    image: obsidiandynamics/kafka   # Broker Kafka simplifié, prêt à l'emploi  
    ports:  
      - "2181:2181"                # Port Zookeeper (nécessaire pour cette version de Kafka)  
      - "9092:9092"                # Port externe pour accéder à Kafka depuis le host  
    environment:  
      # Définition des listeners pour Kafka :  
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"  
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"  
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"  # Permet aux brokers de communiquer entre eux (utile dans un cluster)  

  kafdrop:  
    image: obsidiandynamics/kafdrop  # Interface web pour visualiser les topics et messages  
    ports:  
      - "9000:9000"                  # Port accessible depuis le navigateur  
    environment:  
      KAFKA_BROKERCONNECT: "kafka:29092"  # Adresse du broker Kafka à surveiller  
    depends_on:  
      - kafka                        # Ne démarre qu'après Kafka  

  producer:  
    profiles: ["manual-start"]        # <<< NE SE LANCE PAS PAR DÉFAUT, on le démarre manuellement  
    build: ./producer                 # Construit l'image à partir du Dockerfile dans ./producer  
    depends_on:  
      - kafka                          # Attend que Kafka soit disponible avant de démarrer  
    restart: always                    # Redémarre automatiquement en cas de plantage  
```

:bulb:Explications :  
- On sépare Kafka, Kafdrop et le producteur pour que chaque service soit indépendant et redémarrable individuellement.  
- L’option profiles permet de ne pas lancer le producer automatiquement pour éviter d’envoyer des messages dès le démarrage.  
- depends_on garantit que Kafka est prêt avant le démarrage du producer ou de Kafdrop.  

2) `producer.py`

```python
from kafka import KafkaProducer  
import time  
from datetime import datetime  

# Connexion au broker Kafka  
producer = KafkaProducer(bootstrap_servers='kafka:29092')  
topic = "dpe-topic"  # Nom du topic dans lequel les messages seront envoyés  

while True:  
    msg = f"hello - {datetime.now()}"  # Création d'un message simple avec timestamp  
    producer.send(topic, msg.encode("utf-8"))  # Envoi du message encodé en bytes  
    print("Sent:", msg, flush=True)  # Affichage dans le terminal  
    time.sleep(5)  # Attente de 5 secondes avant le prochain message  
```

:bulb:Explications :  
- Ce script montre le principe de **producteur Kafka** : envoi continu de messages vers un topic.  
- On encode les messages en utf-8 car Kafka n’accepte que des bytes.  
- On peut observer chaque message en temps réel dans **Kafdrop**.  

3) `Dockerfile`

```dockerfile
# Base Python 3.10
FROM python:3.10
# Répertoire de travail dans le container
WORKDIR /app
# Copie du script producer dans le container
COPY producer.py .
# Installation de la librairie Kafka pour Python
RUN pip install kafka-python
# Commande à exécuter au démarrage du container 
CMD ["python", "producer.py"]  
```

:bulb:Explications :  
- Ce Dockerfile permet de **containeriser le producteur**, pour qu’il fonctionne de manière isolée.  
- Toute modification du script nécessite un rebuild pour que le container prenne en compte les changements.  

### Commandes Docker

```sh
# Arrêter et supprimer tous les containers, volumes inclus  
docker compose down -v  

# Démarrer Kafka et Kafdrop en arrière-plan  
docker compose up -d  

# Accéder à Kafdrop via navigateur : http://localhost:9000  

# Lancer le producer manuellement  
docker compose --profile manual-start up -d  

# Vérifier les containers en cours  
docker container ps  

# Voir les logs du producer en direct  
docker logs -f <container-name-producer>

# Arrêter le producer  
docker compose --profile manual-start down  

# Rebuild du producer si le script Python a été modifié  
docker compose --profile manual-start up -d --build  

# Arrêter tous les services  
docker compose down  
docker container ps  
```

:bulb:Points à retenir :  
- Docker permet de **simuler un environnement Kafka complet** sans installer Kafka localement.  
- Kafdrop facilite la **visualisation des messages**, ce qui rend les concepts abstraits plus concrets.  
- La séparation des services illustre le **découplage producteur / broker / consommateur**, principe clé de Kafka.


## Exercice 2 : Envoyer des données depuis une API vers Kafka

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Récupérer des données depuis une **API publique** et les envoyer vers Kafka.  
- Utiliser **KafkaProducer** pour publier des messages JSON sur un topic.  
- Observer les messages dans **Kafdrop** et comprendre comment Kafka peut servir de **pipeline pour des données en temps réel**.  

Cet exercice illustre un **cas concret d’usage** : ingestion de données externes (ici les DPE) pour analyse ou traitement en temps réel.

### Arborescence

```txt
kafka-exo2
 ├── docker-compose.yml
 ├── producer/
 │    ├── producer.py
 │    └── Dockerfile
```

1) `docker-compose.yml`

```yaml
services:
  kafka:
    image: obsidiandynamics/kafka   # Broker Kafka simplifié
    ports:
      - "2181:2181"                # Port Zookeeper
      - "9092:9092"                # Port Kafka pour le host
    environment:
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"

  kafdrop:
    image: obsidiandynamics/kafdrop  # Interface web pour visualiser les topics
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
    depends_on:
      - kafka

  producer:
    profiles: ["manual-start"]        # Ne se lance pas par défaut, à démarrer manuellement
    build: ./producer                 # Construire l'image à partir du Dockerfile
    depends_on:
      - kafka
    restart: always                    # Redémarre automatiquement en cas de plantage
```

:bulb:Explications :  
- On sépare **Kafka**, **Kafdrop** et le **producer** pour une isolation claire et une meilleure compréhension du flux des données.  
- Le producer est lancé manuellement pour contrôler le moment où les données commencent à être envoyées.  
- Kafdrop permet d’inspecter les messages envoyés et de vérifier que les données ont bien été publiées dans le topic.

2) `Dockerfile`

```dockerfile
FROM python:3.10               # Base Python 3.10
WORKDIR /app                    # Répertoire de travail dans le container
COPY producer.py .              # Copie du script producer
RUN pip install kafka-python requests  # Installation des librairies nécessaires
ENV PYTHONUNBUFFERED=1          # Affichage direct des logs
CMD ["python", "producer.py"]   # Commande à exécuter au démarrage
```

:bulb:Explications :  
- La librairie **requests** est utilisée pour appeler l’API publique.  
- **kafka-python** permet d’envoyer les messages vers Kafka.  
- `PYTHONUNBUFFERED=1` assure que les prints s’affichent immédiatement dans les logs Docker.

3) `producer.py`

```python
import requests
from kafka import KafkaProducer
import time
import json

API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

# Création du producer Kafka avec sérialisation JSON
producer = KafkaProducer(
    bootstrap_servers='kafka:29092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

topic = "dpe-topic"

page = 1
size = 5

while True:
    params = {
        "page": page,
        "size": size,
        "select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69 AND date_reception_dpe:[2024-01-01 TO 2024-12-31]"
    }

    try:
        response = requests.get(API_URL, params=params)  # Appel à l'API
        data = response.json()
        lines = data.get("results", [])

        if not lines:
            print("Aucune donnée reçue, nouvelle tentative dans 10s", flush=True)
        else:
            for line in lines:
                producer.send(topic, line)  # Envoi de chaque ligne vers Kafka
                print("Sent:", line, flush=True)
            page += 1

    except Exception as e:
        print("Erreur API ou Kafka:", e, flush=True)

    time.sleep(10)  # Pause avant la prochaine requête
```

:bulb:Explications :  
- Ce script illustre un **pipeline Kafka en temps réel** : récupération d’une API → publication sur Kafka.  
- Chaque message est envoyé en **JSON**, ce qui est standard pour le traitement de données.  
- On peut observer les messages dans **Kafdrop** pour comprendre le flux producteur → broker.

### Commandes Docker

```sh
# Arrêter les services existants
docker compose down

# Démarrer Kafka et Kafdrop
docker compose up -d kafka kafdrop

# Créer le topic "dpe-topic" dans Kafdrop via l'interface http://localhost:9000

# Lancer le producer manuellement et rebuild si nécessaire
docker compose --profile manual-start up -d --build

# Vérifier les containers
docker container ps

# Suivre les logs du producer en direct
docker logs -f <container-name-producer>

# Arrêter le producer
docker stop <container-name-producer>

# Arrêter tous les services
docker compose down
docker container ps
```

:bulb:Points à retenir :  
- Kafka permet de **centraliser des flux de données externes** pour traitement ou analyse.  
- Kafdrop facilite la **visualisation des messages JSON**.  
- On comprend comment intégrer une source externe (API) dans un pipeline Kafka.  
- La séparation des services illustre le **découplage producteur / broker / consommateur**, principe clé de Kafka.



## Exercice 3 : Pipeline complet Kafka avec Producer et Consumer

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Mettre en place un **pipeline Kafka complet** avec un **producer** qui récupère des données depuis une API et un **consumer** qui les lit depuis Kafka.  
- Observer les messages dans **Kafdrop** et sur le consumer pour comprendre le flux complet producteur → broker → consommateur.  
- Illustrer la notion de **consumer group** et la lecture depuis le début d’un topic.  

### Arborescence

```txt
kafka-exo3
 ├── docker-compose.yml
 ├── producer/
 │    ├── producer.py
 │    └── Dockerfile
 └── consumer/
      ├── consumer.py
      └── Dockerfile
```

1) `docker-compose.yml`

```yaml
services:
  kafka:
    image: obsidiandynamics/kafka
    ports:
      - "2181:2181"               # Port Zookeeper
      - "9092:9092"               # Port Kafka exposé pour le host
    environment:
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"
      KAFKA_CREATE_TOPICS: "dpe-topic:1:1"  # Création automatique du topic à l'initialisation

  kafdrop:
    image: obsidiandynamics/kafdrop
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
    depends_on:
      - kafka

  producer:
    profiles: ["manual-start"]      # Ne se lance pas automatiquement
    build: ./producer
    depends_on:
      - kafka
    restart: always

  consumer:
    profiles: ["manual-start"]      # Ne se lance pas automatiquement
    build: ./consumer
    depends_on:
      - kafka
    environment:
      KAFKA_BROKER: kafka:29092
      KAFKA_TOPIC: dpe-topic
    restart: always
```

:bulb:Explications :  
- La variable `KAFKA_CREATE_TOPICS` permet de créer automatiquement le topic `dpe-topic` pour éviter une étape manuelle.  
- Les services **producer** et **consumer** sont sur des profils manuels pour que l’enseignant contrôle le démarrage et l’ordre des flux.  
- Kafdrop sert à visualiser les messages envoyés sur le topic.  


2) `consumer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY consumer.py .
RUN pip install kafka-python
CMD ["python", "consumer.py"]
```

:bulb:Explications :  
- Le consumer est containerisé pour fonctionner indépendamment.  
- `kafka-python` permet la connexion et la lecture des messages depuis Kafka.

3) `consumer/consumer.py`

```python
from kafka import KafkaConsumer
import json
import os

KAFKA_BROKER = os.environ.get("KAFKA_BROKER", "kafka:29092")
KAFKA_TOPIC = os.environ.get("KAFKA_TOPIC", "dpe-topic")

consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    auto_offset_reset='earliest',  # Lire depuis le début du topic
    group_id='dpe-consumer-group',
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

print(f"Consumer listening on topic '{KAFKA_TOPIC}'")

for message in consumer:
    print("Received:", message.value)
```

:bulb:Explications :  
- `auto_offset_reset='earliest'` permet au consumer de lire tous les messages depuis le début, même si le topic contient déjà des données.  
- `group_id` illustre le concept de **consumer group**, essentiel pour Kafka pour répartir les messages entre plusieurs consumers.  


4) `producer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY producer.py .
RUN pip install kafka-python requests
ENV PYTHONUNBUFFERED=1
CMD ["python", "producer.py"]
```

:bulb:Explications :  
- La librairie `requests` est utilisée pour appeler l’API ADEME et récupérer les données DPE.  
- `PYTHONUNBUFFERED=1` assure que les logs s’affichent en temps réel dans Docker.

5) `producer/producer.py`

```python
import requests
from kafka import KafkaProducer
import time
import json

API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

producer = KafkaProducer(
    bootstrap_servers='kafka:29092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

topic = "dpe-topic"

page = 1
size = 5

while True:
    params = {
        "page": page,
        "size": size,
        "select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69 AND date_reception_dpe:[2024-01-01 TO 2024-12-31]"
    }

    try:
        response = requests.get(API_URL, params=params)
        data = response.json()
        lines = data.get("results", [])

        if not lines:
            print("Aucune donnée reçue, nouvelle tentative dans 10s", flush=True)
        else:
            for line in lines:
                producer.send(topic, line)
                print("Sent:", line, flush=True)
            page += 1

    except Exception as e:
        print("Erreur API ou Kafka:", e, flush=True)

    time.sleep(10)
```

:bulb:Explications :  
- Ce producer illustre un **pipeline Kafka complet** : récupération des données externes → publication dans un topic.  
- Les messages sont envoyés en JSON et peuvent être consommés par plusieurs consumers.  


### Commandes Docker

```sh
# Démarrage de Kafka et Kafdrop en arrière-plan
docker compose up -d kafka kafdrop
```

:bulb:Explications :  
- On démarre d’abord Kafka et Kafdrop pour que le broker soit prêt et le topic visible dans l’interface web.

```sh
# Lancer les services producer et consumer avec rebuild si nécessaire
docker compose --profile manual-start up -d --build
```

:bulb:Explications :  
- Les containers producer et consumer sont lancés manuellement pour contrôler le flux des messages.  
- `--build` assure que les dernières modifications du code Python sont prises en compte.

```sh
# Vérifier les containers en cours
docker container ps
```

:bulb:Explications :  
- Permet de confirmer que tous les containers sont démarrés correctement.

```sh
# Suivre les logs du producer
docker logs -f <container-name-producer>

# Suivre les logs du consumer
docker logs -f <container-name-consumer>
```

:bulb:Explications :  
- Les logs montrent le flux des messages envoyés et reçus.  
- On peut observer en temps réel le **pipeline producteur → broker → consommateur**.

```sh
# Arrêter les containers
docker stop <container-name-producer>
docker stop <container-name-consumer>
docker compose down
docker container ps
```

:bulb:Explications :  
- Arrêt propre des containers pour libérer les ressources.  
- `docker compose down` supprime le réseau et les containers liés, utile avant de relancer un nouvel exercice.


## Exercice 4 : Kafka → PostgreSQL avec Producer et Consumer

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Créer un pipeline Kafka complet où le **producer** récupère des données depuis une API et le **consumer** les écrit dans une base **PostgreSQL**.  
- Observer les messages dans **Kafdrop** et vérifier leur insertion dans PostgreSQL.  
- Comprendre comment Kafka peut servir de **bus de données** pour stocker des informations dans une base relationnelle.  

### Arborescence

```txt
kafka-exo4
 ├── docker-compose.yml
 ├── producer/
 │    ├── producer.py
 │    └── Dockerfile
 └── consumer/
      ├── consumer.py
      └── Dockerfile
```

1) `docker-compose.yml`

```yaml
services:
  kafka:
    image: obsidiandynamics/kafka
    ports:
      - "2181:2181"               # Port Zookeeper
      - "9092:9092"               # Port Kafka exposé
    environment:
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"
      KAFKA_CREATE_TOPICS: "dpe-topic:1:1"  # Création automatique du topic

  kafdrop:
    image: obsidiandynamics/kafdrop
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
    depends_on:
      - kafka

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      POSTGRES_DB: dpe
    ports:
      - "5432:5432"

  producer:
    profiles: ["manual-start"]      # Lancement manuel
    build: ./producer
    depends_on:
      - kafka
    restart: always

  consumer:
    profiles: ["manual-start"]      # Lancement manuel
    build: ./consumer
    depends_on:
      - kafka
      - postgres
    environment:
      KAFKA_BROKER: kafka:29092
      KAFKA_TOPIC: dpe-topic
      POSTGRES_HOST: postgres
      POSTGRES_DB: dpe
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
    restart: always
```

:bulb:Explications :  
- `KAFKA_CREATE_TOPICS` crée le topic `dpe-topic` automatiquement pour simplifier l’exercice.  
- PostgreSQL est ajouté pour montrer comment Kafka peut alimenter une base de données relationnelle.  
- Les services producer et consumer sont manuels pour qu'on puisse contrôler l’ordre de lancement.  


2) `producer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY producer.py .
RUN pip install kafka-python requests
ENV PYTHONUNBUFFERED=1
CMD ["python", "producer.py"]
```

3) `producer/producer.py`

```python
import requests
from kafka import KafkaProducer
import time
import json

API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

producer = KafkaProducer(
    bootstrap_servers='kafka:29092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

topic = "dpe-topic"
page = 1
size = 5

while True:
    params = {
        "page": page,
        "size": size,
        "select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69 AND date_reception_dpe:[2024-01-01 TO 2024-12-31]"
    }

    try:
        response = requests.get(API_URL, params=params)
        data = response.json()
        lines = data.get("results", [])

        if not lines:
            print("Aucune donnée reçue, nouvelle tentative dans 10s", flush=True)
        else:
            for line in lines:
                producer.send(topic, line)
                print("Sent:", line, flush=True)
            page += 1

    except Exception as e:
        print("Erreur API ou Kafka:", e, flush=True)

    time.sleep(10)
```

:bulb:Explications :  
- Le producer récupère des données externes et les publie dans Kafka.  
- JSON est utilisé comme format standard pour le flux de données.  
- `PYTHONUNBUFFERED=1` assure l’affichage direct des logs dans Docker.  


4) `consumer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY consumer.py .
RUN pip install kafka-python psycopg2-binary
CMD ["python", "consumer.py"]
```

5) `consumer/consumer.py`

```python
from kafka import KafkaConsumer
import psycopg2
import json
import os

# Kafka config
KAFKA_BROKER = os.environ.get("KAFKA_BROKER", "kafka:29092")
KAFKA_TOPIC = os.environ.get("KAFKA_TOPIC", "dpe-topic")

# PostgreSQL config
POSTGRES_HOST = os.environ.get("POSTGRES_HOST", "postgres")
POSTGRES_DB = os.environ.get("POSTGRES_DB", "dpe")
POSTGRES_USER = os.environ.get("POSTGRES_USER", "admin")
POSTGRES_PASSWORD = os.environ.get("POSTGRES_PASSWORD", "admin")

# Connexion PostgreSQL
conn = psycopg2.connect(
    host=POSTGRES_HOST,
    dbname=POSTGRES_DB,
    user=POSTGRES_USER,
    password=POSTGRES_PASSWORD
)
cur = conn.cursor()

# Création de la table si elle n'existe pas
cur.execute("""
CREATE TABLE IF NOT EXISTS dpe_data (
    numero_dpe TEXT,
    date_reception_dpe DATE,
    code_postal_ban TEXT,
    etiquette_dpe TEXT
)
""")
conn.commit()

# Consumer Kafka
consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    auto_offset_reset='earliest',
    group_id='dpe-consumer-group',
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

print(f"Consumer listening on topic '{KAFKA_TOPIC}'")

for message in consumer:
    data = message.value
    print("Received:", data)
    # Insertion dans PostgreSQL
    cur.execute("""
    INSERT INTO dpe_data (numero_dpe, date_reception_dpe, code_postal_ban, etiquette_dpe)
    VALUES (%s, %s, %s, %s)
    """, (
        data.get("numero_dpe"),
        data.get("date_reception_dpe"),
        data.get("code_postal_ban"),
        data.get("etiquette_dpe")
    ))
    conn.commit()
```

:bulb:Explications :  
- Le consumer lit les messages depuis Kafka et les insère dans PostgreSQL.  
- La table `dpe_data` est créée si nécessaire pour stocker les informations.  
- Ce flux illustre le **pipeline Kafka → base de données**.  


### Commandes Docker

```sh
# Démarrer Kafka et Kafdrop
docker compose up -d kafka kafdrop
```

:bulb:Explications :  
- On démarre d’abord Kafka et Kafdrop pour que le broker soit prêt et que le topic soit visible dans l’interface web.

```sh
# Lancer PostgreSQL
docker compose up -d postgres
```

:bulb:Explications :  
- PostgreSQL doit être disponible avant le consumer pour que la connexion réussisse.

```sh
# Se connecter à PostgreSQL pour vérifier la table
docker exec -it <container-name-postgres> psql -U admin -d dpe
CREATE TABLE IF NOT EXISTS dpe_data (
    numero_dpe TEXT,
    date_reception_dpe DATE,
    code_postal_ban TEXT,
    etiquette_dpe TEXT
);
exit
```

:bulb:Explications :  
- Création manuelle ou vérification de la table avant de lancer le consumer.

```sh
# Lancer le producer et le consumer manuellement avec rebuild
docker compose --profile manual-start up -d --build
```

:bulb:Explications :  
- Le producer commence à envoyer des messages vers Kafka, le consumer les lit et les stocke dans PostgreSQL.

```sh
# Vérifier les containers en cours
docker container ps
```

```sh
# Suivre les logs du producer
docker logs -f <container-name-producer>

# Suivre les logs du consumer
docker logs -f <container-name-consumer>
```

:bulb:Explications :  
- Les logs permettent de suivre le flux de données et de vérifier que l’insertion dans PostgreSQL fonctionne.

```sh
# Vérifier les données insérées dans PostgreSQL
docker exec -it <container-name-postgres> psql -U admin -d dpe
SELECT * FROM dpe_data LIMIT 10;
SELECT count(*) FROM dpe_data;
```

:bulb:Explications :  
- Permet de confirmer que les messages Kafka ont bien été persistés dans la base.

```sh
# Arrêter les containers
docker stop <container-name-producer>
docker stop <container-name-consumer>
docker compose down
```

:bulb:Explications :  
- Arrêt propre de tous les services pour libérer les ressources et préparer un nouvel exercice.


## Exercice 5 : Kafka avec plusieurs partitions et scaling des consumers

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Travailler avec un **topic Kafka à plusieurs partitions** pour illustrer la répartition des messages entre consumers.  
- Mettre en place un **producer** qui récupère des données depuis l’API ADEME et un **consumer** qui écrit dans PostgreSQL.  
- Observer le comportement de Kafka lorsque **plusieurs instances de consumer** lisent le même topic (consumer group).  
- Comprendre comment Kafka gère le **load balancing** et la tolérance aux pannes.

### Arborescence

```txt
kafka-exo5
 ├── docker-compose.yml
 ├── producer/
 │    ├── producer.py
 │    └── Dockerfile
 └── consumer/
      ├── consumer.py
      └── Dockerfile
```

1) `docker-compose.yml`

```yaml
services:
  kafka:
    image: obsidiandynamics/kafka
    ports:
      - "2181:2181"               # Port Zookeeper
      - "9092:9092"               # Port Kafka exposé
    environment:
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"
      KAFKA_CREATE_TOPICS: "dpe-topic:4:1"  # Topic avec 4 partitions

  kafdrop:
    image: obsidiandynamics/kafdrop
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
    depends_on:
      - kafka

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      POSTGRES_DB: dpe
    ports:
      - "5432:5432"

  producer:
    build: ./producer
    depends_on:
      - kafka
    restart: always

  consumer:
    build: ./consumer
    depends_on:
      - kafka
      - postgres
    environment:
      KAFKA_BROKER: kafka:29092
      KAFKA_TOPIC: dpe-topic
      POSTGRES_HOST: postgres
      POSTGRES_DB: dpe
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
    restart: always
```

:bulb:Explications :  
- Le topic `dpe-topic` a **4 partitions** pour montrer la distribution des messages entre plusieurs consumers.  
- Plusieurs instances de consumer peuvent lire le topic en même temps grâce au **consumer group**, Kafka répartit les partitions automatiquement.  
- Kafdrop permet de visualiser la répartition des messages par partition.  


2) `producer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY producer.py .
RUN pip install kafka-python requests
ENV PYTHONUNBUFFERED=1
CMD ["python", "producer.py"]
```

3) `producer/producer.py`

```python
import requests
from kafka import KafkaProducer
import time
import json

API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

producer = KafkaProducer(
    bootstrap_servers='kafka:29092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

topic = "dpe-topic"

page = 1
size = 5

while True:
    params = {
        "page": page,
        "size": size,
        "select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69 AND date_reception_dpe:[2024-01-01 TO 2024-12-31]"
    }

    try:
        response = requests.get(API_URL, params=params)
        data = response.json()
        lines = data.get("results", [])

        if not lines:
            print("Aucune donnée reçue, nouvelle tentative dans 10s", flush=True)
        else:
            for line in lines:
                producer.send(topic, line)
                print("Sent:", line, flush=True)
            page += 1  # Pagination pour récupérer les pages suivantes

    except Exception as e:
        print("Erreur API ou Kafka:", e, flush=True)

    time.sleep(10)
```

:bulb:Explications :  
- Chaque message est envoyé sur le topic, Kafka le répartira sur les 4 partitions.  
- `page += 1` permet d’avancer dans les pages de l’API pour simuler un flux continu de données.  


4) `consumer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY consumer.py .
RUN pip install kafka-python psycopg2-binary
CMD ["python", "consumer.py"]
```

5) `consumer/consumer.py`

```python
from kafka import KafkaConsumer
import psycopg2
import json
import os

# Kafka config
KAFKA_BROKER = os.environ.get("KAFKA_BROKER", "kafka:29092")
KAFKA_TOPIC = os.environ.get("KAFKA_TOPIC", "dpe-topic")

# PostgreSQL config
POSTGRES_HOST = os.environ.get("POSTGRES_HOST", "postgres")
POSTGRES_DB = os.environ.get("POSTGRES_DB", "dpe")
POSTGRES_USER = os.environ.get("POSTGRES_USER", "admin")
POSTGRES_PASSWORD = os.environ.get("POSTGRES_PASSWORD", "admin")

# Connexion Postgres
conn = psycopg2.connect(
    host=POSTGRES_HOST,
    dbname=POSTGRES_DB,
    user=POSTGRES_USER,
    password=POSTGRES_PASSWORD
)
cur = conn.cursor()

# Création de la table
cur.execute("""
CREATE TABLE IF NOT EXISTS dpe_data (
    numero_dpe TEXT,
    date_reception_dpe TEXT,
    code_postal_ban TEXT,
    etiquette_dpe TEXT
)
""")
conn.commit()

# Consumer
consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    auto_offset_reset='earliest',
    group_id='dpe-consumer-group',
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

print("Consumer started...")

for msg in consumer:
    print(f"[CONSUMER] Partition={msg.partition} Offset={msg.offset} → {msg.value}")

    data = msg.value

    cur.execute("""
        INSERT INTO dpe_data (numero_dpe, date_reception_dpe, code_postal_ban, etiquette_dpe)
        VALUES (%s, %s, %s, %s)
    """, (
        data.get("numero_dpe"),
        data.get("date_reception_dpe"),
        data.get("code_postal_ban"),
        data.get("etiquette_dpe")
    ))
    conn.commit()
```

:bulb:Explications :  
- Les logs montrent la **partition et l’offset** pour illustrer comment Kafka répartit les messages.  
- Plusieurs consumers peuvent consommer simultanément, mais chaque partition est lue par un seul consumer dans le groupe.  
- L’insertion dans PostgreSQL permet de suivre le flux de données persistant.  


### Commandes Docker

```sh
# Démarrer Kafka, PostgreSQL et Kafdrop
docker compose up -d kafka postgres kafdrop
```

:bulb:Explications :  
- Kafka et PostgreSQL doivent être prêts avant de lancer les producteurs et consumers.  
- Kafdrop permet de visualiser les partitions du topic.

```sh
# Créer manuellement le topic avec 4 partitions si nécessaire via Kafdrop
```

```sh
# Lancer le producer
docker compose up -d producer
docker container ps
```

:bulb:Explications :  
- Le producer commence à envoyer les messages et les répartit sur les partitions du topic.

```sh
# Créer la table dans PostgreSQL
docker exec -it <container-name-postgres> psql -U admin -d dpe
CREATE TABLE IF NOT EXISTS dpe_data (
    numero_dpe TEXT,
    date_reception_dpe DATE,
    code_postal_ban TEXT,
    etiquette_dpe TEXT
);
```

```sh
# Lancer plusieurs instances de consumer pour tester le scaling
docker compose up -d --scale consumer=2
docker container ps
```

:bulb:Explications :  
- Kafka attribue automatiquement une partition à chaque consumer dans le groupe pour **équilibrer la charge**.  
- Les messages d’une même partition ne sont jamais envoyés à plusieurs consumers simultanément.

```sh
# Vérifier le nombre de messages insérés dans PostgreSQL
docker exec -it <container-name-postgres> psql -U admin -d dpe
SELECT count(*) FROM dpe_data;
```

```sh
# Observer les logs des consumers
docker logs -f <container-name-consumer-1>
docker logs -f <container-name-consumer-2>
```

:bulb:Explications :  
- Permet de voir comment les messages sont consommés en parallèle par les deux consumers.

```sh
# Tester la tolérance : arrêter un consumer et vérifier que l’autre prend le relais
docker stop <container-name-consumer-1>
docker logs -f <container-name-consumer-2>

# Vérifier le nombre de messages restant dans PostgreSQL
docker exec -it <container-name-postgres> psql -U admin -d dpe
SELECT count(*) FROM dpe_data;
```

:bulb:Explications :  
- Kafka assure la **tolérance aux pannes** : si un consumer tombe, l’autre reprend la lecture des partitions.  

```sh
# Arrêter tous les services et supprimer les volumes
docker compose down -v
```

:bulb:Explications :  
- Permet de nettoyer l’environnement avant un nouvel exercice ou une nouvelle session.


## Exercice 6 : Kafka avec consommateurs spécialisés (DB + Analytics)

### Objectif de l’exercice

Dans cet exercice, nous allons :  
- Démontrer le **découplage des consumers** avec Kafka : un consumer écrit dans une base PostgreSQL et un autre effectue des analyses statistiques.  
- Utiliser un **topic Kafka à plusieurs partitions** pour simuler un flux de données réel.  
- Observer comment Kafka gère **plusieurs groupes de consumers** consommant le même topic.  
- Illustrer la séparation des responsabilités : stockage persistant vs traitement analytique en temps réel.

### Arborescence

```txt
kafka-exo6
 ├── docker-compose.yml
 ├── producer/
 │    ├── producer.py
 │    └── Dockerfile
 └── consumer/
      ├── consumer_db.py
      ├── consumer_analytics.py
      └── Dockerfile
```


1) `docker-compose.yml`

```yaml
services:
  kafka:
    image: obsidiandynamics/kafka
    ports:
      - "2181:2181"               # Port Zookeeper
      - "9092:9092"               # Port Kafka exposé
    environment:
      KAFKA_LISTENERS: "INTERNAL://:29092,EXTERNAL://:9092"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka:29092,EXTERNAL://localhost:9092"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"
      KAFKA_CREATE_TOPICS: "dpe-topic:4:1"  # Topic avec 4 partitions

  kafdrop:
    image: obsidiandynamics/kafdrop
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: "kafka:29092"
    depends_on:
      - kafka

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      POSTGRES_DB: dpe
    ports:
      - "5432:5432"

  producer:
    build: ./producer
    depends_on:
      - kafka
    restart: always

  consumer_db:
    build: ./consumer
    container_name: consumer_db
    command: ["python", "consumer_db.py"]
    depends_on:
      - kafka
      - postgres
    environment:
      KAFKA_BROKER: kafka:29092
      KAFKA_TOPIC: dpe-topic
      POSTGRES_HOST: postgres
      POSTGRES_DB: dpe
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      CONSUMER_GROUP: dpe-db-writer
    restart: always

  consumer_analytics:
    build: ./consumer
    container_name: consumer_analytics
    command: ["python", "consumer_analytics.py"]
    depends_on:
      - kafka
    environment:
      KAFKA_BROKER: kafka:29092
      KAFKA_TOPIC: dpe-topic
      CONSUMER_GROUP: dpe-analytics
    restart: always
```

:bulb:Explications :  
- Deux consommateurs distincts illustrent la **puissance des consumer groups** Kafka : un pour la **persistance**, l’autre pour l’**analytique**.  
- Chaque consumer peut lire le même topic indépendamment sans interférer.  
- PostgreSQL stocke les données pour des requêtes ultérieures, tandis que l’analytics effectue du calcul et déclenche des alertes.


2) `producer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY producer.py .
RUN pip install kafka-python requests
ENV PYTHONUNBUFFERED=1
CMD ["python", "producer.py"]
```

3) `producer/producer.py`

```python
import requests
from kafka import KafkaProducer
import time
import json

API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

producer = KafkaProducer(
    bootstrap_servers='kafka:29092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

topic = "dpe-topic"

page = 1
size = 5

while True:
    params = {
        "page": page,
        "size": size,
        "select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69 AND date_reception_dpe:[2024-01-01 TO 2024-12-31]"
    }

    try:
        response = requests.get(API_URL, params=params)
        data = response.json()
        lines = data.get("results", [])

        if not lines:
            print("Aucune donnée reçue, nouvelle tentative dans 10s", flush=True)
        else:
            for line in lines:
                producer.send(topic, line)
                print("Sent:", line, flush=True)
    except Exception as e:
        print("Erreur API ou Kafka:", e, flush=True)

    time.sleep(10)
```

:bulb:Explications :  
- Le producer envoie les DPE vers Kafka.  
- Kafka répartit les messages sur les partitions pour permettre un traitement parallèle par plusieurs consumers.  


4) `consumer/Dockerfile`

```dockerfile
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install kafka-python psycopg2-binary
```

5) `consumer/consumer_db.py`

```python
from kafka import KafkaConsumer
import psycopg2
import json
import os

KAFKA_BROKER = os.environ.get("KAFKA_BROKER", "kafka:29092")
KAFKA_TOPIC = os.environ.get("KAFKA_TOPIC", "dpe-topic")
CONSUMER_GROUP = os.environ.get("CONSUMER_GROUP", "dpe-db-writer")

POSTGRES_HOST = os.environ.get("POSTGRES_HOST", "postgres")
POSTGRES_DB = os.environ.get("POSTGRES_DB", "dpe")
POSTGRES_USER = os.environ.get("POSTGRES_USER", "admin")
POSTGRES_PASSWORD = os.environ.get("POSTGRES_PASSWORD", "admin")

conn = psycopg2.connect(
    host=POSTGRES_HOST,
    dbname=POSTGRES_DB,
    user=POSTGRES_USER,
    password=POSTGRES_PASSWORD
)
cur = conn.cursor()
cur.execute("""
CREATE TABLE IF NOT EXISTS dpe_data (
    numero_dpe TEXT,
    date_reception_dpe TEXT,
    code_postal_ban TEXT,
    etiquette_dpe TEXT
)
""")
conn.commit()

consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    auto_offset_reset='earliest',
    group_id=CONSUMER_GROUP,
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

print(f"[{CONSUMER_GROUP}] Consumer started...")

for msg in consumer:
    data = msg.value
    cur.execute("""
        INSERT INTO dpe_data (numero_dpe, date_reception_dpe, code_postal_ban, etiquette_dpe)
        VALUES (%s, %s, %s, %s)
    """, (
        data.get("numero_dpe"),
        data.get("date_reception_dpe"),
        data.get("code_postal_ban"),
        data.get("etiquette_dpe")
    ))
    conn.commit()
    print(f"[DB] Inserted: {data}")
```

6) `consumer/consumer_analytics.py`

```python
from kafka import KafkaConsumer
import json
import os
from collections import defaultdict

KAFKA_BROKER = os.environ.get("KAFKA_BROKER", "kafka:29092")
KAFKA_TOPIC = os.environ.get("KAFKA_TOPIC", "dpe-topic")
CONSUMER_GROUP = os.environ.get("CONSUMER_GROUP", "dpe-analytics")

count_by_day = defaultdict(int)
etiquette_count = defaultdict(int)

consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    auto_offset_reset='earliest',
    group_id=CONSUMER_GROUP,
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

print(f"[{CONSUMER_GROUP}] Analytics consumer started...")

for msg in consumer:
    data = msg.value
    day = data.get("date_reception_dpe", "unknown")[:10]
    etiquette = data.get("etiquette_dpe", "unknown")

    count_by_day[day] += 1
    etiquette_count[etiquette] += 1

    if etiquette in ["F", "G"]:
        print(f"[ALERT] DPE critique reçu: {data}")

    if sum(count_by_day.values()) % 10 == 0:
        print(f"[STATS] DPE par jour: {dict(count_by_day)}")
        print(f"[STATS] Répartition étiquettes: {dict(etiquette_count)}")
```


### Commandes Docker

```sh
docker compose down -v
docker compose up -d kafka postgres kafdrop
# Accéder à Kafdrop pour créer manuellement le topic dpe-topic avec 4 partitions
docker compose up -d producer
docker container ps
```

:bulb:Explications : le producer envoie les messages vers Kafka, visibles dans Kafdrop.

```sh
docker compose up -d consumer_db
docker exec -it <container-name-postgres> psql -U admin -d dpe
SELECT count(*) FROM dpe_data;
docker logs -f consumer_db
```

:bulb:Explications : le consumer_db écrit dans PostgreSQL et montre l’insertion des messages.

```sh
docker compose up -d consumer_analytics
docker logs -f consumer_analytics
```

:bulb:Explications : le consumer_analytics calcule des statistiques et alerte sur les DPE critiques.

```sh
docker compose down -v
```

:bulb:Explications : nettoyage complet des containers et volumes pour repartir sur un environnement propre.



## Mots-clés et notions essentielles du TP Kafka avec Docker

- **Kafka** : plateforme de streaming distribuée, permet d’ingérer, stocker et traiter des flux de données en temps réel.  
- **Broker** : serveur Kafka qui stocke et distribue les messages.  
- **Topic** : canal de communication où les messages sont publiés par les producteurs et lus par les consommateurs.  
- **Partition** : subdivision d’un topic, permettant le parallélisme et la scalabilité.  
- **Producer (Producteur)** : composant qui envoie des messages à un topic Kafka.  
- **Consumer (Consommateur)** : composant qui lit les messages d’un topic.  
- **Consumer Group** : groupe de consommateurs qui se partagent les partitions d’un topic pour traiter les messages en parallèle.  
- **Offset** : position d’un message dans une partition, permet au consumer de savoir quel message a été lu.  
- **Kafdrop** : interface web pour visualiser les topics, partitions et messages Kafka.  
- **Topic multi-partitions** : permet aux consommateurs d’un même groupe de lire les messages en parallèle.  
- **Découplage producteur/consommateur** : principe clé de Kafka, producteur et consommateur sont indépendants.  
- **Tolérance aux pannes / réplication** : Kafka peut répliquer les partitions pour assurer la durabilité et la disponibilité des messages.  
- **Serialization / Deserialization** : transformation des messages en bytes pour Kafka (json.dumps / json.loads).  
- **Docker profiles** : permet de contrôler quels services se lancent automatiquement ou manuellement.  
- **Scaling des consumers** : augmentation du nombre de consommateurs pour paralléliser le traitement d’un topic multi-partitions.  

## Aller plus loin avec Kafka

Après avoir suivi ce TP, plusieurs pistes permettent d’approfondir vos connaissances et expérimenter des fonctionnalités avancées de Kafka :

- **Dead Letter Queue (DLQ)** :  
  Permet de rediriger les messages non traitables vers un topic spécifique pour éviter de bloquer le traitement principal.  
  Utile pour gérer les erreurs de parsing ou les messages corrompus.

- **Contrôle d’accès et sécurité (ACL / SASL / SSL)** :  
  Kafka propose des mécanismes pour sécuriser l’accès aux topics, contrôler quels producteurs/consommateurs peuvent lire ou écrire, et chiffrer les messages en transit.

- **Replication et tolérance aux pannes avancée** :  
  Expérimenter avec plusieurs brokers, partitions et facteurs de réplication pour simuler un cluster résilient et tolérant aux pannes.

- **Monitoring et métriques** :  
  Intégrer des outils comme **Prometheus**, **Grafana** ou les métriques JMX pour suivre les performances du cluster et détecter les problèmes.

- **Image officielle Kafka vs image simplifiée** :  
  - L’image utilisée dans ce TP (`obsidiandynamics/kafka`) est allégée et pratique pour un TP, mais limitée (pas de configuration avancée, cluster multi-broker simplifié).  
  - L’image officielle Apache Kafka (`confluentinc/cp-kafka` ou `bitnami/kafka`) permet de créer un **cluster complet**, gérer la sécurité, la réplication et utiliser les connecteurs Kafka Connect.

- **Kafka Connect & Kafka Streams** :  
  - **Kafka Connect** : pour connecter Kafka à des bases de données, fichiers ou systèmes externes sans écrire de code.  
  - **Kafka Streams** : pour effectuer des transformations et agrégations complexes directement dans Kafka, en temps réel.

- **Test de scalabilité** :  
  Expérimenter le **scaling horizontal** avec plusieurs producteurs et consumers, pour observer la répartition des partitions et l’équilibrage de charge.

:bulb:Ces axes permettent de passer d’un TP pédagogique vers une utilisation **production-ready** de Kafka, en intégrant la sécurité, la fiabilité et le traitement de flux plus complexes.



# Mini-projet Kafka : Ingestion des données DPE par département

## Objectif pédagogique

Ce mini-projet vise à mettre en pratique les concepts Kafka, Docker et PostgreSQL étudiés dans le TP avec :

- Lancer **plusieurs producteurs Kafka**, chacun ciblant un département spécifique via une variable d’environnement.  
- Utiliser un **consumer unique** pour ingérer les messages dans une base PostgreSQL en batch de 100 lignes.  
- Gérer la **pagination et la fin de flux** de l’API ADEME, afin que les producteurs s’arrêtent automatiquement lorsqu’il n’y a plus de données.  
- Persister le contenu final de la base PostgreSQL dans un **volume Docker** pour conserver les résultats après l’arrêt des containers.  

Ce projet combine **Kafka, multi-producer, batch processing, PostgreSQL et volumes Docker**.

## Consignes générales

1. **Paramétrage par département**  
   - Fournir une liste de départements à traiter (ex. : 69, 01, 38)  
   - Chaque producer doit récupérer son département via une variable d’environnement (`DEPARTMENT`)  

2. **Gestion des producteurs Kafka**  
   - Construire une image Docker pour le producer  
   - Lancer un conteneur par département, chaque conteneur interrogeant l’API ADEME  
   - Pagination : récupérer **500 lignes à chaque appel API**  
   - Stop automatique du producer : si l’API retourne une erreur ou aucune donnée, le producer s’arrête  

3. **Consumer unique et ingestion batch**  
   - Construire un consumer unique pour Kafka, connecté à PostgreSQL  
   - Les messages sont stockés dans la base en **batch de 100 lignes** pour améliorer les performances  
   - La base PostgreSQL peut contenir une table `dpe_data` avec les colonnes : `numero_dpe`, `date_reception_dpe`, `code_postal_ban`, `etiquette_dpe`, `departement`

4. **Persistance des données**  
   - Créer un **volume Docker** pour la base PostgreSQL afin de conserver les données après l’arrêt des containers  
   - Vérifier que les données sont accessibles en exécutant une requête simple (`SELECT count(*) FROM dpe_data;`)  

5. **Organisation et bonnes pratiques**  
   - Les producteurs et le consumer doivent être indépendants et isolés  
   - Rebuild nécessaire si le script Python ou le Dockerfile change  
   - Les producteurs peuvent s’exécuter **en parallèle**, chaque département dans son propre conteneur  
   - Le consumer doit pouvoir ingérer les messages de tous les producteurs simultanément  

## Points visés

- Gestion de **multi-producer Kafka** avec une seule instance de consumer  
- Traitement **batch** et ingestion dans PostgreSQL  
- Pagination et gestion de la **fin de flux API**  
- Persistance des données avec **volumes Docker**  
- Mise en pratique complète du workflow **API → Kafka → Consumer → Base de données**  
- Isolation et orchestration via **Docker Compose**  
- Approche **scalable et robuste**, applicable à de vrais cas d’usage en entreprise

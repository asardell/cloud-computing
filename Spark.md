#  Spark

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Apache_Spark_logo.svg/langfr-330px-Apache_Spark_logo.svg.png" alt="Source de l'image" width="600"/>
</p>

- [Spark](#spark)
  - [Qu’est-ce que Spark ?](#quest-ce-que-spark-)
  - [Architecture de Spark](#architecture-de-spark)
    - [Le Driver](#le-driver)
    - [Les Workers](#les-workers)
  - [Concepts clés](#concepts-clés)
    - [Parallelisation](#parallelisation)
    - [DataFrames et RDDs](#dataframes-et-rdds)
    - [Lazy evaluation](#lazy-evaluation)
    - [Spark et Python (PySpark)](#spark-et-python-pyspark)
    - [Spark vs Python classique](#spark-vs-python-classique)
    - [Spark et le stockage](#spark-et-le-stockage)
- [TP : Mettre en place un cluster Apache Spark](#tp--mettre-en-place-un-cluster-apache-spark)
  - [Contexte](#contexte)
  - [Arborescence du projet](#arborescence-du-projet)
  - [Interfaces Web Spark](#interfaces-web-spark)
  - [Commandes Docker](#commandes-docker)
  - [Points clés](#points-clés)

Dans ce cours, vous allez découvrir **Apache Spark**, un moteur de calcul distribué incontournable dans le domaine du **Big Data**.  

Vous avez jusqu’à présent manipulé des données avec :
- **Python** et des librairies comme **Pandas**
- **MinIO** pour le stockage objet

Spark vous permettra de **passer à l’échelle** et de traiter de grands volumes de données en parallèle sur plusieurs cœurs ou machines.

## Qu’est-ce que Spark ?

Apache Spark est un **moteur de traitement de données distribué** qui permet de :
- Exploiter **tous les cœurs CPU d’une machine**
- Distribuer le calcul sur **plusieurs machines**
- Gérer les données en **mémoire pour accélérer les traitements**

Spark est utilisé pour :
- L’analyse de logs
- Les pipelines de traitement de données
- Le **Machine Learning**
- La gestion de **Data Lakes** (données brutes, transformées, et finales)

:bulb: L’idée est de ne pas charger tout le dataset en mémoire comme avec Pandas, mais de le **découper en partitions** et de traiter chaque partition de manière parallèle.

## Architecture de Spark

Spark suit une architecture **maître / esclaves** appelée **Driver / Workers** :

### Le Driver

Le **Driver** est le processus principal :
- Il lance l’application Spark
- Il découpe le travail en **tâches distribuables**
- Il coordonne l’exécution sur les Workers
- Il reçoit les résultats partiels et calcule le résultat final

### Les Workers

Les **Workers (ou Executors)** exécutent le travail :
- Ils reçoivent les tâches depuis le Driver
- Ils traitent les partitions de données **en parallèle**
- Ils renvoient les résultats au Driver

Ensemble, Driver + Workers forment un **cluster Spark**, que ce soit sur une machine unique ou sur plusieurs serveurs.

<p align="center">
  <img src="https://spark.apache.org/docs/latest/img/cluster-overview.png" alt="Source de l'image" width="600"/>
</p>

## Concepts clés

### Parallelisation

Spark divise automatiquement vos données en **partitions** :
- Chaque partition est traitée indépendamment sur un Worker
- Les transformations et actions Spark sont appliquées **en parallèle**
- Cela permet d’accélérer considérablement les calculs

Exemple concret :
- Dataset de 10 Go → 10 partitions → 10 Workers traitant les partitions en même temps

### DataFrames et RDDs

Spark propose deux principales structures de données :
- **RDD (Resilient Distributed Dataset)** : données immuables et distribuées, tolérantes aux pannes
- **DataFrame** : abstractions tabulaires sur RDD, similaires aux tables SQL ou aux DataFrames Pandas, plus faciles pour l’analyse

:bulb: Les DataFrames sont généralement préférés pour leur simplicité et leur intégration avec SQL.

### Lazy evaluation

- Les transformations Spark sont **lazy**, c’est-à-dire qu’elles **ne s’exécutent pas immédiatement**
- L’exécution réelle se fait lorsqu’une **action** est demandée (ex : `show()`, `count()`, `write()`)

Cela permet à Spark d’optimiser les calculs et de minimiser les lectures/écritures sur disque.

### Spark et Python (PySpark)

Spark peut être utilisé avec Python via **PySpark** :
- Vous écrivez votre code en Python
- Spark exécute le traitement en **distribué** sans que vous ayez à gérer les threads ou le parallélisme

### Spark vs Python classique

| Python (Pandas)       | Spark                        |
|----------------------|------------------------------|
| Exécution locale       | Distribuée sur cluster       |
| Limité par la RAM      | Scalabilité horizontale      |
| Séquentiel             | Parallélisé sur plusieurs Workers |
| Adapté aux petits volumes | Adapté aux gros volumes de données |


### Spark et le stockage

Spark ne stocke pas les données par lui-même : il se connecte à des systèmes de stockage pour lire et écrire les données.

:bulb: Avec Spark, contrairement à un script Python classique, vous pouvez **écrire directement sur un bucket MinIO ou S3**, sans passer par des fichiers locaux temporaires.


# TP : Mettre en place un cluster Apache Spark

## Contexte

L'objectif de ce TP est de vous permettre de **découvrir Apache Spark** et de le tester en environnement multi-workers sans avoir à l'installer localement. Nous allons :

1. Construire une **image Docker personnalisée** pour Spark.
2. Déployer un cluster avec un **Master**, un ou plusieurs **Workers**, et un **History Server**.
3. Soumettre des jobs Spark (PySpark) et observer la **parallélisation** et l'exécution via les interfaces web.

## Arborescence du projet

```
spark-docker/
├── Dockerfile
├── docker-compose.yml
├── spark-defaults.conf
├── test_job.py
├── test_job_interact.py
├── example_parallel.py
```

- `Dockerfile` : construit l'image Spark avec Java et Python.
- `docker-compose.yml` : définit le cluster (Master, Workers, History Server).
- `spark-defaults.conf` : configuration Spark pour le Master et History Server.
- fichiers `.py` : exemple de job PySpark à soumettre sur le cluster.

1. docker-compose.yml

```yaml
networks:
  sparkpg_network:

volumes:
  spark-events:

services:
  spark-master:
    build: .
    container_name: spark-master
    networks:
      - sparkpg_network
    entrypoint: /bin/bash
    command: -c "/opt/spark/sbin/start-master.sh; tail -f /dev/null"
    ports:
      - '8080:8080'
      - '4040:4040'
    volumes:
      - spark-events:/opt/spark/events

  spark-worker:
    build: .
    container_name: spark-worker1
    networks:
      - sparkpg_network
    entrypoint: /bin/bash
    command: -c "/opt/spark/sbin/start-worker.sh spark://spark-master:7077; tail -f /dev/null"
    volumes:
      - spark-events:/opt/spark/events  

  spark-worker2:
    build: .
    container_name: spark-worker2
    networks:
      - sparkpg_network
    entrypoint: /bin/bash
    command: -c "/opt/spark/sbin/start-worker.sh spark://spark-master:7077; tail -f /dev/null"
    volumes:
      - spark-events:/opt/spark/events  

  spark-history-server:
    build: .
    container_name: spark-history-server
    networks:
      - sparkpg_network
    entrypoint: /bin/bash
    command: -c "/opt/spark/sbin/start-history-server.sh; tail -f /dev/null"
    volumes:
      - spark-events:/opt/spark/events 
    ports:
      - '18080:18080'
```

**Explications :**  
- `tail -f /dev/null` permet de garder les conteneurs actifs après le lancement de Spark.  
- `spark-events` volume partagé pour que le History Server lise les logs des Workers.  
- Avec 2 Workers, les jobs soumis via `spark-submit` seront **répartis automatiquement** entre eux.


2. Dockerfile

```dockerfile
FROM python:3.11-bullseye

# Installer Java
RUN apt-get update \
    && apt-get install -y --no-install-recommends openjdk-17-jdk \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Variables d'environnement
ENV SPARK_HOME="/opt/spark"
ENV JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
ENV PATH="${JAVA_HOME}:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${PATH}"
ENV SPARK_MASTER_PORT="7077"
ENV SPARK_MASTER_HOST="spark-master"

# Créer le répertoire SPARK_HOME
RUN mkdir -p ${SPARK_HOME}
WORKDIR ${SPARK_HOME}

# Télécharger et installer Spark (dernière version 3.5.x)
RUN curl -s https://dlcdn.apache.org/spark/ | grep -oP 'spark-3\.5\.[0-9]+' | sort -V | tail -1 | \
    xargs -I {} curl -O https://dlcdn.apache.org/spark/{}/{}-bin-hadoop3.tgz \
    && tar xvzf spark-3.5.*-bin-hadoop*.tgz --directory ${SPARK_HOME} --strip-components 1 \
    && rm -rf spark-3.5.*-bin-hadoop*.tgz

# Copier la configuration Spark
COPY ./spark-defaults.conf "${SPARK_HOME}/conf"

# Entrypoint bash
ENTRYPOINT ["/bin/bash"]
```

**Explications :**  
- On installe Java car Spark en dépend.  
- On définit les variables d'environnement `SPARK_HOME`, `JAVA_HOME` et `PATH` pour pouvoir exécuter Spark depuis n'importe où dans le conteneur.  
- La ligne `curl` récupère dynamiquement la dernière version 3.5.x de Spark.  
- L’ENTRYPOINT bash permet de lancer les scripts Spark depuis le conteneur.

3. `spark-defaults.conf`

```conf
# URL du master
spark.master                           spark://spark-master:7077

# Activation de l’event log
spark.eventLog.enabled                 true
spark.eventLog.dir                     /opt/spark/events

# Configuration du History Server
spark.history.fs.logDirectory          /opt/spark/events
```

**Explications :**  
- Permet aux Workers et au History Server de connaître le Master.  
- Active la journalisation des jobs pour les visualiser dans le History Server.

4. `test_job.py`

```python
from pyspark.sql import SparkSession

# Créer une session Spark
spark = SparkSession.builder \
    .appName("TestJob") \
    .master("spark://spark-master:7077") \
    .getOrCreate()

# Exemple simple : créer un DataFrame et faire un calcul
data = [("Alice", 10), ("Bob", 20), ("Charlie", 30)]
columns = ["name", "value"]

df = spark.createDataFrame(data, columns)

# Faire une opération simple : somme des valeurs
total = df.groupBy().sum("value").collect()[0][0]

print(f"Somme des valeurs : {total}")

# Fermer la session Spark
spark.stop()
```


5. `test_job_interact.py`

```python
from pyspark.sql import SparkSession
import time

# Créer une session Spark
spark = SparkSession.builder \
    .appName("TestJobInteractive") \
    .master("spark://spark-master:7077") \
    .getOrCreate()

# Exemple : créer un DataFrame
data = [(i,) for i in range(1, 101)]
columns = ["number"]
df = spark.createDataFrame(data, columns)

# Faire un calcul un peu plus long pour observer le job
result = df.groupBy().sum("number")

print("Calcul en cours...")
time.sleep(10)  # Pause pour garder le job en cours quelques secondes

# Afficher le résultat
total = result.collect()[0][0]
print(f"Somme des nombres de 1 à 100 : {total}")

# Pause finale pour voir le job dans le Web UI
time.sleep(15)

# Fermer la session Spark
spark.stop()
```

6. `example_parallel.py`

```python
from pyspark.sql import SparkSession

# Créer une session Spark connectée au Master
spark = SparkSession.builder \
    .appName("ExampleParallel") \
    .master("spark://spark-master:7077") \
    .getOrCreate()

# Créer un DataFrame de 100 nombres
data = [(i,) for i in range(1, 101)]
columns = ["number"]
df = spark.createDataFrame(data, columns)

# Repartir en 4 partitions pour paralléliser
df = df.repartition(4)

# Calcul : somme des nombres
total_sum = df.groupBy().sum("number").collect()[0][0]

print(f"Somme des nombres de 1 à 100 : {total_sum}")

# Fermer la session Spark
spark.stop()
```

**Explications :**  
- `repartition(4)` : divise les données en 4 tâches, qui seront distribuées sur les Workers.  
- Même avec 2 Workers, Spark répartira automatiquement les 4 partitions pour paralléliser le traitement.

## Interfaces Web Spark

- Spark Master UI : [http://localhost:8080](http://localhost:8080) → voir les Workers connectés et leur ressources.
- Spark Web UI : [http://localhost:4040](http://localhost:4040) → voir l’exécution des jobs en cours.
- Spark History Server : [http://localhost:18080](http://localhost:18080) → consulter les jobs terminés.

## Commandes Docker

1. **Se placer dans le dossier du projet** :
```bash
cd ./spark-docker
```

2. **Construire l’image Docker** :
```bash
docker-compose build
```

3. **Démarrer le cluster en arrière-plan** :
```bash
docker-compose up -d
```

4. **Vérifier les conteneurs actifs** :
```bash
docker ps
```

5. **Copier le script PySpark dans le conteneur Master** :
```bash
docker exec -it spark-master /bin/bash
ls
exit
docker cp test_job.py spark-master:/opt/spark/test_job.py
```

6. **Soumettre le job Spark** :
```bash
docker exec -it spark-master /bin/bash
ls
spark-submit /opt/spark/test_job.py
exit
```

7. **Copier le script PySpark dans le conteneur Master** :
```bash
docker exec -it spark-master /bin/bash
ls
exit
docker cp test_job_interact.py spark-master:/opt/spark/test_job_interact.py
```

8. **Soumettre le job Spark** :
```bash
docker exec -it spark-master /bin/bash
ls
spark-submit /opt/spark/test_job_interact.py
exit
```
http://localhost:4040 → voir l’exécution des jobs en cours.

9. **Copier le script PySpark dans le conteneur Master** :
```bash
docker exec -it spark-master /bin/bash
ls
exit
docker cp example_parallel.py spark-master:/opt/spark/example_parallel.py
```

10. **Soumettre le job Spark** :
```bash
docker exec -it spark-master /bin/bash
ls
spark-submit /opt/spark/example_parallel.py
exit
```

http://localhost:4040 → voir l’exécution des jobs en cours.


11. **Arrêter le cluster** :
```bash
docker-compose down
```

## Points clés

- Spark répartit automatiquement les tâches sur tous les Workers connectés.
- Même avec un seul Worker, il y a parallélisation grâce aux **cores disponibles**.
- Avec plusieurs Workers, les jobs sont mieux parallélisés et les performances augmentent.
- `docker-compose` simplifie la gestion du cluster et la reproductibilité de l’environnement.
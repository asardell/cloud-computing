#  MinIO

<p align="center">
  <img src="https://blog.min.io/content/images/2019/05/MINIO_wordmark.png" alt="Source de l'image" width="600"/>
</p>

- [MinIO](#minio)
- [Introduction à MinIO](#introduction-à-minio)
  - [Pourquoi MinIO en entreprise et pour le cloud souverain ?](#pourquoi-minio-en-entreprise-et-pour-le-cloud-souverain-)
    - [Concurrents principaux](#concurrents-principaux)
  - [Concepts clés](#concepts-clés)
    - [Buckets](#buckets)
    - [Stockage de fichiers vs SGBD](#stockage-de-fichiers-vs-sgbd)
    - [Data Lake et couches Medaillon](#data-lake-et-couches-medaillon)
  - [Formats de fichiers](#formats-de-fichiers)
    - [Pourquoi le partitionnement est essentiel en Data Lake ?](#pourquoi-le-partitionnement-est-essentiel-en-data-lake-)
    - [Cardinalité : notion clé](#cardinalité--notion-clé)
    - [Bonne pratique](#bonne-pratique)
    - [Mauvais exemple](#mauvais-exemple)
    - [Pourquoi c’est stratégique en entreprise ?](#pourquoi-cest-stratégique-en-entreprise-)
    - [Astuces : Lecture d'un fichier parquet](#astuces--lecture-dun-fichier-parquet)
  - [Apache Iceberg](#apache-iceberg)
    - [Qu’est-ce qu’Apache Iceberg ?](#quest-ce-quapache-iceberg-)
    - [Pourquoi ACID est important ?](#pourquoi-acid-est-important-)
    - [Pourquoi Iceberg est intéressant](#pourquoi-iceberg-est-intéressant)
    - [Cas d’usage typique](#cas-dusage-typique)
    - [Conclusion](#conclusion)
  - [MinIO et Python](#minio-et-python)
  - [TD : MinIO et données ADEME](#td--minio-et-données-ademe)
    - [Arborescence du TP](#arborescence-du-tp)
    - [docker-compose.yml](#docker-composeyml)
      - [1. Service `minio`](#1-service-minio)
      - [2. Service `python`](#2-service-python)
    - [requirements.txt](#requirementstxt)
    - [Dockerfile](#dockerfile)
    - [Script – `fetch_ademe_to_minio.py`](#script--fetch_ademe_to_miniopy)
    - [Script – `json_to_csv.py`](#script--json_to_csvpy)
    - [Script – `csv_to_parquet.py`](#script--csv_to_parquetpy)
    - [Commandes docker](#commandes-docker)
    - [Résultat  attendu](#résultat--attendu)
    - [Tester la persistance des données MinIO avec les volumes Docker](#tester-la-persistance-des-données-minio-avec-les-volumes-docker)
  - [TD : Gestion des utilisateurs et policies](#td--gestion-des-utilisateurs-et-policies)
    - [Arborescence du projet](#arborescence-du-projet)
    - [ubuntu/Dockerfile](#ubuntudockerfile)
    - [policies/read-policy.json](#policiesread-policyjson)
    - [policies/write-policy.json](#policieswrite-policyjson)
    - [python/test\_users.py](#pythontest_userspy)
    - [Commandes à exécuter](#commandes-à-exécuter)
  - [TD : MinIO et Apache Iceberg](#td--minio-et-apache-iceberg)
    - [Arborescence du projet](#arborescence-du-projet-1)
    - [Configuration Spark](#configuration-spark)
    - [Commandes Docker expliquées](#commandes-docker-expliquées)


# Introduction à MinIO

**Objectif :** Découvrir MinIO, comprendre ses concepts clés et son utilité pour le stockage d’objets dans le cloud, en particulier pour des projets pédagogiques et le cloud souverain.

MinIO est un **système de stockage d’objets** compatible avec l’API S3 d’AWS.  
Il permet de stocker et récupérer des fichiers (objets) de manière simple et rapide, à petite ou grande échelle.

## Pourquoi MinIO en entreprise et pour le cloud souverain ?

- **Compatibilité S3** : fonctionne comme AWS S3, donc facile à intégrer dans des projets Python, Spark, ou Hadoop via **Boto3** ou d’autres clients S3.  
- **Cloud souverain** : solution open-source adaptée aux besoins de souveraineté numérique. Les données restent sur l’infrastructure locale ou nationale.  
- **Pédagogique et gratuite** : idéal pour apprendre le stockage d’objets, manipuler des buckets et tester des pipelines de données sans coût cloud.  
- **Scalabilité** : peut gérer de petits projets comme des volumes massifs de données.  

### Concurrents principaux

| Solution | Particularité |
|----------|---------------|
| AWS S3 | Stockage d’objets cloud leader, robuste, riche en fonctionnalités |
| Azure Storage Account | Stockage cloud Microsoft, intégration avec Azure Data Factory |
| Google Cloud Storage | Stockage cloud Google, compatible S3 via certains outils |
| MinIO | Open-source, léger, compatible S3, déployable localement ou en cloud souverain |


## Concepts clés

### Buckets

- Un **bucket** est un conteneur logique pour stocker des objets (fichiers).  
- Chaque objet a une **clé** unique dans le bucket.  
- On peut avoir plusieurs buckets pour organiser les données par projet, type ou environnement.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1200/1*edAFf8onuLXdRGGn2Uu5jQ.png" alt="Source de l'image" width="600"/>
</p>


### Stockage de fichiers vs SGBD

- MinIO stocke des fichiers, contrairement aux SGBD qui stockent des **données structurées** dans des tables.  
- Idéal pour les **fichiers volumineux**, les logs, les images, vidéos, JSON, CSV, Parquet…  
- Les fichiers peuvent être lus par des outils de traitement de données, ETL, Spark, ou Pandas.

<p align="center">
  <img src="https://preprod.leviia.com/wp-content/uploads/2023/07/stockage-objet-bucket-s3.png" alt="Source de l'image" width="600"/>
</p>


### Data Lake et couches Medaillon

- MinIO est souvent utilisé pour constituer un **Data Lake**, c’est-à-dire un entrepôt de données centralisé qui accepte tous types de formats.  
- Exemple de couches Medaillon :
  - **Bronze** : données brutes, telles qu’elles sont collectées
  - **Silver** : données nettoyées, enrichies, prêtes pour analyse
  - **Gold** : données consolidées et optimisées pour reporting ou machine learning

<p align="center">
  <img src="https://www.databricks.com/sites/default/files/inline-images/building-data-pipelines-with-delta-lake-120823.png" alt="Source de l'image" width="600"/>
</p>

## Formats de fichiers

| Format | Particularité | Usage recommandé |
|--------|---------------|----------------|
| JSON | Semi-structuré, flexible, mais volumineux | Données brutes ou API |
| CSV | Structuré, simple | Transformation et échange de données |
| Parquet | Colonnaire, compressé, partitionnable | Analytics, data lake, Big Data |
  
<p align="center">
  <img src="https://substackcdn.com/image/fetch/$s_!4vTf!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7b4d4edc-20dd-4480-8b33-e11705fcd61f_2000x1428.png" alt="Source de l'image" width="600"/>
</p>

Le **partitionnement** consiste à **découper un jeu de données en plusieurs sous-dossiers** en fonction de la valeur d’une ou plusieurs colonnes.

Exemple :

```
final/parquet/
 ├── etiquette_dpe=A/
 ├── etiquette_dpe=B/
 ├── etiquette_dpe=C/
 └── etiquette_dpe=D/
```

Chaque dossier contient uniquement les lignes correspondant à la valeur de la partition.

### Pourquoi le partitionnement est essentiel en Data Lake ?

Sans partitionnement :
- le moteur de calcul (Spark, Pandas, DuckDB…) doit lire **tout le dataset**

Avec partitionnement :
- seules les partitions utiles sont lues  --> Gain énorme en **temps de calcul** et en **coût**


### Cardinalité : notion clé

La **cardinalité** correspond au **nombre de valeurs distinctes** d’une colonne.

Types de cardinalité

| Type | Exemple | Caractéristique |
|------|---------|-----------------|
| Faible cardinalité ✅ | `etiquette_dpe` (A, B, C, D, E) | Très peu de valeurs |
| Moyenne | `code_departement` (01 → 95) | Plusieurs dizaines |
| Haute cardinalité ❌ | `id_unique`, `numero_dpe` | Quasi unique par ligne |

### Bonne pratique

On **partitionne de préférence sur des colonnes à faible cardinalité**, comme :

- catégories (`etiquette_dpe`)
- pays
- département
- type de produit
- date

### Mauvais exemple

Partitionner sur :

- un identifiant unique
- un numéro de facture
- une clé primaire

Cela crée des **milliers de dossiers vides ou à une seule ligne**, ce qui dégrade les performances.


### Pourquoi c’est stratégique en entreprise ?

Le partitionnement est utilisé massivement dans :

- Data Lakes AWS S3
- Azure Data Lake
- Google Cloud Storage
- Spark
- Hive
- Databricks

Il permet de :

- accélérer les requêtes  
- réduire les coûts de lecture  
- mieux organiser les données  

### Astuces : Lecture d'un fichier parquet

Les fichiers **Parquet** sont **colonnaires et optimisés pour le traitement Big Data**, mais ils ne sont pas facilement lisibles dans un simple ou éditeur de texte classique.  

Pour visualiser ou explorer un fichier Parquet localement, on peut utiliser des outils **open source** comme [Parquet Viewer](https://github.com/mukunku/ParquetViewer). Une application graphique pour ouvrir et explorer les fichiers Parquet.

:bulb: [Télécharger le fichier `ParquetViewer.exe`](https://github.com/mukunku/ParquetViewer/releases/download/v3.5.0.2/ParquetViewer.exe)


<p align="center">
  <img src="https://github.com/mukunku/ParquetViewer/raw/main/wiki_images/main_screenshot5.png" alt="Source de l'image" width="600"/>
</p>


## Apache Iceberg

<p align="center">
  <img src="https://cdn.prod.website-files.com/60f955236a773f743298d63b/64ba8c146aed466249378cb1_AnyConv.com__image6.webp" alt="Source de l'image" width="600"/>
</p>


### Qu’est-ce qu’Apache Iceberg ?

Apache Iceberg est un **format de table open source pour les Data Lakes** qui apporte :

- Une **gestion ACID complète** (Atomicité, Cohérence, Isolation, Durabilité) sur des fichiers stockés dans des systèmes distribués (S3, ADLS, GCS…).  
- La possibilité de **travailler sur des tables immuables avec des snapshots**, ce qui rend les opérations comme **update, delete, merge** possibles sur un Data Lake.  
- Une compatibilité avec des moteurs de calcul comme **Spark, Flink, Trino, Hive**.

### Pourquoi ACID est important ?

ACID est un acronyme pour :

| Lettre | Signification | Pourquoi c’est utile |
|--------|---------------|--------------------|
| A | Atomicité | Chaque transaction est **tout ou rien** : aucune donnée partiellement écrite |
| C | Cohérence | Les données restent **cohérentes** après chaque transaction |
| I | Isolation | Plusieurs jobs ou utilisateurs peuvent écrire/lire simultanément **sans conflit** |
| D | Durabilité | Une fois une transaction validée, elle est **persistante**, même en cas de panne |

Sans ACID, les Data Lakes peuvent devenir **instables** lorsqu’on fait des mises à jour ou des suppressions, ce qui peut provoquer des **données corrompues ou incohérentes**.

### Pourquoi Iceberg est intéressant

1. **Gestion efficace des fichiers**  
   - Iceberg maintient un **catalogue de métadonnées**.  
   - Permet d’éviter de scanner **des milliers de fichiers** à chaque requête.  
   - Compatible avec **partitionnement et clustering**.

2. **Support ACID sur un Data Lake**  
   - Les tables Iceberg permettent les **UPDATE, DELETE, MERGE** alors que des formats classiques comme Parquet ne le permettent pas nativement.  
   - Idéal pour des pipelines de données **modifiables**.

3. **Snapshots et time travel**  
   - On peut revenir à un **ancien état de la table** en quelques lignes de code.  
   - Très utile pour **audits, debugging, et pipelines reproductibles**.

4. **Performance et scalabilité**  
   - Partitionnement intelligent et indexation des fichiers.  
   - Requêtes plus rapides sur de grands datasets même dans le Cloud.

### Cas d’usage typique

- **Data Lake d’entreprise** : stockage de données transactionnelles et analytiques avec possibilité de modifications.  
- **Tables événementielles** : logs, clics web, transactions financières.  
- **Machine Learning** : réentraîner des modèles avec des versions stables des datasets.


### Conclusion

- Iceberg apporte **robustesse et flexibilité** aux Data Lakes.  
- Avec ACID et snapshots, on peut faire du **traitement batch ou streaming** sur des données fiables.  
- Il combine **les avantages des Data Warehouses (fiabilité, cohérence)** et des **Data Lakes (scalabilité, coût réduit)**.


## MinIO et Python

L’**API S3** (Simple Storage Service) est un standard pour le stockage d’objets dans le cloud.  
Elle a été créée par **AWS S3** et est aujourd’hui reprise par de nombreux services :

- Amazon S3 (AWS)
- Azure Blob Storage (via couches de compatibilité)
- Google Cloud Storage
- MinIO

:bulb: Cela signifie qu’un même code peut fonctionner sur plusieurs fournisseurs de cloud.

- Grâce à la **compatibilité S3**, on peut utiliser **Boto3**, le client officiel AWS, pour interagir avec MinIO :  
  - Créer des buckets  
  - Uploader et télécharger des fichiers  
  - Lister ou supprimer des objets  

:bulb: Cela permet  de se familiariser avec le stockage cloud même sans abonnement AWS ou Azure.

<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1200/1*rUUJdOUmInl-lXPq2hO4jA.jpeg" alt="Source de l'image" width="600"/>
</p>

## TD : MinIO et données ADEME

Ce TP permet de mettre en pratique les concepts de **stockage objet**, **buckets**, **formats de fichiers** et **data lake**, tout en utilisant Python et Boto3 dans un environnement local gratuit.
Nous allons manipuler MinIO pour créer un **pipeline de données** basé sur les données publiques de l’ADEME.


Nous utilisons **Docker Compose** pour orchestrer deux services :

- **MinIO** : serveur de stockage d’objets (équivalent local d’AWS S3).
- **Python** : conteneur qui exécutera nos scripts pour :
  - appeler l’API ADEME
  - sauvegarder les données dans MinIO
  - transformer les formats (JSON → CSV → Parquet).

Schéma logique :

```
API ADEME ──> Python ──> MinIO (Bucket)
```

Le projet vise à simuler un **Data Lake local avec MinIO**, structuré en couches :

```
Bucket MinIO
 ├── bronze/   (données brutes JSON)
 ├── silver/   (données nettoyées en CSV)
 └── gold/     (données optimisées en Parquet partitionné)
```

Lien avec AWS S3 & Boto3

MinIO est **compatible S3**, donc :
- Le code que les étudiants apprennent ici peut être réutilisé plus tard sur :
  - AWS S3
  - Azure Blob Storage
  - Google Cloud Storage

C’est pour cela que l’on utilise **Boto3** : c’est une compétence transférable vers le monde professionnel.

### Arborescence du TP

```text
tp-minio-ademe/
├── docker-compose.yml
├── python/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── fetch_ademe_to_minio.py # Récupère les données ADEME et les stocke dans MinIO
│   ├── json_to_csv.py          # Transforme les fichiers JSON en CSV
│   └── csv_to_parquet.py       # Convertit les CSV en Parquet avec partitionnement
```

### docker-compose.yml

```yaml
services: 
  minio:
    image: minio/minio
    container_name: minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    command: server /data --console-address ":9001"
    volumes:
      - minio_data:/data

  python:
    build:
      context: ./python
    container_name: minio-python
    depends_on:
      - minio
    volumes:
      - ./python:/app
    working_dir: /app
    command: python fetch_ademe_to_minio.py

volumes:
  minio_data:
```

Explication

#### 1. Service `minio`

```yaml
image: minio/minio
```
Nous utilisons l’image officielle de MinIO.  
Cela nous permet de simuler un **S3 local**, sans passer par AWS.

```yaml
ports:
  - "9000:9000"
  - "9001:9001"
```
- Port **9000** → API S3 (utilisée par Boto3)
- Port **9001** → Console web MinIO (interface graphique)

👉 Après le lancement, tu peux accéder à l’interface :  
`http://localhost:9001`

```yaml
environment:
  MINIO_ROOT_USER: minioadmin
  MINIO_ROOT_PASSWORD: minioadmin
```
Identifiants pour se connecter à la console MinIO.

```yaml
command: server /data --console-address ":9001"
```
- Démarre le serveur MinIO
- Stocke les fichiers dans `/data`
- Active la console web

```yaml
volumes:
  - minio_data:/data
```
**Point clé :**  
Ce volume permet de **persister les fichiers même si le conteneur est supprimé**.  
C’est très proche du fonctionnement d’un vrai data lake.


#### 2. Service `python`

```yaml
build: .
```
Construit l’image à partir du `Dockerfile`.

```yaml
depends_on:
  - minio
```
Forcer Docker à démarrer MinIO avant le script Python.

```yaml
volumes:
  - ./python:/app
```
Permet de modifier les scripts localement sans reconstruire l’image.

```yaml
command: python fetch_ademe_to_minio.py
```
Lance automatiquement le script de récupération des données ADEME.


### requirements.txt

```text
boto3
requests
pandas
pyarrow
```

Rôle de chaque bibliothèque

- **boto3** : SDK AWS compatible avec MinIO → permet de parler en **langage S3**
- **requests** : appeler l’API ADEME
- **pandas** : manipuler les données sous forme de DataFrame
- **pyarrow** : écrire des fichiers **Parquet**


### Dockerfile

```dockerfile
# Image Python officielle
FROM python:3.11.8-slim

WORKDIR /app

# Copier requirements
COPY requirements.txt /app/requirements.txt

# Installer dépendances
RUN pip install --no-cache-dir -r requirements.txt

# Copier tous les scripts Python
COPY . /app/
```

Explication

```dockerfile
FROM python:3.11.8-slim
```
Image officielle Python, légère et optimisée.

```dockerfile
WORKDIR /app
```
Répertoire de travail par défaut dans le conteneur.

```dockerfile
COPY requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir -r requirements.txt
```
Installation des dépendances.

```dockerfile
COPY . /app/
```
Copie de tous les scripts Python dans le conteneur.

### Script – `fetch_ademe_to_minio.py`

Objectif : récupérer les données depuis l’API ADEME et les stocker en **JSON brut** dans MinIO.

```python
import requests
import json
import boto3
from pathlib import Path

# ------------------------------------------------------------
# CONFIGURATION API ADEME
# ------------------------------------------------------------
# URL de l’API publique ADEME (source de données ouverte)
API_URL = "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"

# Nombre total de pages que l’on veut récupérer
# (important pour ne pas surcharger l’API)
TOTAL_PAGES = 20

# Nombre de lignes récupérées par page
PAGE_SIZE = 1000

# ------------------------------------------------------------
# CONFIGURATION MINIO (stockage objet type S3)
# ------------------------------------------------------------

# Adresse du serveur MinIO dans le réseau Docker
# IMPORTANT : ici on utilise le nom du service ("minio") du docker-compose
MINIO_ENDPOINT = "http://minio:9000"

# Identifiants (définis dans docker-compose.yml)
MINIO_ACCESS_KEY = "minioadmin"
MINIO_SECRET_KEY = "minioadmin"

# Nom du bucket (équivalent d’un "dossier racine" dans un Data Lake)
BUCKET_NAME = "ademe-data"

# ------------------------------------------------------------
# Création du client S3 compatible MinIO avec Boto3
# ------------------------------------------------------------
s3_client = boto3.client(
    "s3",                            # type de service
    endpoint_url=MINIO_ENDPOINT,     # URL de MinIO
    aws_access_key_id=MINIO_ACCESS_KEY,
    aws_secret_access_key=MINIO_SECRET_KEY
)

# ------------------------------------------------------------
# Création automatique du bucket s’il n’existe pas encore
# ------------------------------------------------------------
buckets = [b["Name"] for b in s3_client.list_buckets()["Buckets"]]

if BUCKET_NAME not in buckets:
    print(f"Création du bucket {BUCKET_NAME}")
    s3_client.create_bucket(Bucket=BUCKET_NAME)

# ------------------------------------------------------------
# FONCTIONS UTILES
# ------------------------------------------------------------

def fetch_page(page: int):
    """
    Récupère les données d'une page ADEME

    - On appelle l’API
    - On filtre uniquement les champs utiles
    - On limite le périmètre au département 69
    """
    params = {
        "size": PAGE_SIZE,
        "page": page,
        #"select": "numero_dpe,date_reception_dpe,code_postal_ban,etiquette_dpe",
        "qs": "code_departement_ban:69"
    }

    # Appel HTTP de l’API
    r = requests.get(API_URL, params=params)

    # Gestion d'erreur simple : si l’API ne renvoie pas du JSON
    try:
        return r.json().get("results", [])
    except ValueError:
        print(f"Erreur JSON pour la page {page}, contenu brut: {r.text[:200]}")
        return []

def save_to_minio(data, filename):
    """
    Sauvegarde des données dans MinIO (bucket S3)

    - Bucket = data lake
    - Key = chemin logique du fichier dans le bucket
    - Body = contenu JSON
    """
    s3_client.put_object(
        Bucket=BUCKET_NAME,
        Key=filename,                                      # ex: raw/page_1.json
        Body=json.dumps(data, ensure_ascii=False, indent=2),
        ContentType="application/json"
    )
    print(f"{filename} envoyé dans le bucket {BUCKET_NAME}")

# ------------------------------------------------------------
# BOUCLE PRINCIPALE (Zone Bronze du Data Lake)
# ------------------------------------------------------------

# Cette boucle va construire la couche "Bronze" :
# → données brutes, non transformées, stockées telles quelles
for page in range(1, TOTAL_PAGES + 1):
    print(f"Récupération page {page}...")

    # Récupération de la page API
    results = fetch_page(page)

    # Si des données existent, on les sauvegarde
    if results:
        # "raw/" correspond à la couche Bronze du Data Lake
        filename = f"raw/page_{page}.json"
        save_to_minio(results, filename)
    else:
        print(f"Aucune donnée pour la page {page}")
```

### Script – `json_to_csv.py`

Objectif : lire les JSON stockés dans MinIO et produire un **CSV consolidé** (couche Silver).

```python
import json
import boto3
import pandas as pd
from io import BytesIO

# ------------------------------------------------------------
# Configuration MinIO
# ------------------------------------------------------------
MINIO_ENDPOINT = "http://minio:9000"
ACCESS_KEY = "minioadmin"
SECRET_KEY = "minioadmin"
BUCKET_NAME = "ademe-data"

# Connexion S3 vers MinIO
s3 = boto3.client(
    "s3",
    endpoint_url=MINIO_ENDPOINT,
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY
)

# Liste des objets dans la zone "raw/" (Bronze)
objects = s3.list_objects_v2(Bucket=BUCKET_NAME, Prefix="raw/")

# Liste Python qui va contenir toutes les lignes
all_rows = []

# ------------------------------------------------------------
# Lecture de tous les fichiers JSON
# ------------------------------------------------------------
for obj in objects.get("Contents", []):
    key = obj["Key"]

    # On ne lit que les fichiers JSON
    if key.endswith(".json"):
        print(f"Lecture {key}")

        # Téléchargement du fichier depuis MinIO
        body = s3.get_object(Bucket=BUCKET_NAME, Key=key)["Body"].read()

        # Conversion JSON → Python
        data = json.loads(body)

        # Ajout des lignes dans la liste globale
        all_rows.extend(data)

# ------------------------------------------------------------
# Transformation en DataFrame Pandas
# ------------------------------------------------------------
df = pd.DataFrame(all_rows)

# Sauvegarde du CSV en mémoire (pas sur disque)
csv_buffer = BytesIO()
df.to_csv(csv_buffer, index=False)

# ------------------------------------------------------------
# Upload dans la couche "Silver"
# ------------------------------------------------------------
s3.put_object(
    Bucket=BUCKET_NAME,
    Key="processed/ademe_all.csv",     # Zone Silver
    Body=csv_buffer.getvalue(),
    ContentType="text/csv"
)

print("CSV créé et uploadé ✅")
```

### Script – `csv_to_parquet.py`

Objectif : produire un **Parquet partitionné**, couche Gold du Data Lake.

```python
import json
import boto3
import pandas as pd
import pyarrow as pa
import pyarrow.parquet as pq
from io import BytesIO
import os

# ------------------------------------------------------------
# Configuration MinIO
# ------------------------------------------------------------
MINIO_ENDPOINT = "http://minio:9000"
ACCESS_KEY = "minioadmin"
SECRET_KEY = "minioadmin"
BUCKET_NAME = "ademe-data"

s3 = boto3.client(
    "s3",
    endpoint_url=MINIO_ENDPOINT,
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY
)

# ------------------------------------------------------------
# Lecture du fichier CSV (Silver)
# ------------------------------------------------------------

obj = s3.get_object(
    Bucket=BUCKET_NAME,
    Key="processed/ademe_all.csv"
)

df = pd.read_csv(BytesIO(obj["Body"].read()))

# Conversion Pandas → Table PyArrow
table = pa.Table.from_pandas(df)

# ------------------------------------------------------------
# Ecriture locale du Parquet partitionné
# ------------------------------------------------------------
# Chaque valeur unique de "etiquette_dpe" aura son propre dossier
temp_folder = "/tmp/ademe_parquet/"

pq.write_to_dataset(
    table,
    root_path=temp_folder,
    partition_cols=["etiquette_dpe"]
)

# ------------------------------------------------------------
# Upload des fichiers Parquet vers MinIO (couche Gold)
# ------------------------------------------------------------
for root, dirs, files in os.walk(temp_folder):
    for file in files:
        local_path = os.path.join(root, file)

        # Chemin relatif dans MinIO
        relative_path = os.path.relpath(local_path, temp_folder)

        # Clé finale S3
        s3_key = f"final/parquet/{relative_path}"

        # Lecture du fichier local et upload dans MinIO
        with open(local_path, "rb") as f:
            s3.put_object(Bucket=BUCKET_NAME, Key=s3_key, Body=f.read())

        print(f"Upload {s3_key} ✅")

print("Parquet partitionné créé et uploadé ✅")
```

**Explication :**
`PyArrow.write_to_dataset()` ne supporte pas directement l’upload S3
La fonction `pq.write_to_dataset`est conçue pour écrire sur un filesystem local ou compatible (type HDFS, S3 via fsspec, etc.).
Si tu voulais écrire directement sur MinIO, il faudrait configurer un filesystem S3 compatible avec fsspec ou Spark. Cela complique le script et nécessite souvent des dépendances supplémentaires.

### Commandes docker

Avant de lancer les scripts Python, il faut construire et démarrer l’infrastructure MinIO + Python.

1. Construction des images Docker

```bash
cd ./tp-minio-ademe
docker compose build
```

**Explication :**
Cette commande :
- lit le `Dockerfile`
- installe Python et les dépendances (`boto3`, `requests`, `pandas`, `pyarrow`)
- prépare l’image du conteneur Python

2. Démarrage des services

```bash
docker compose up -d
```

**Explication :**
- `up` : démarre les conteneurs
- `-d` (detached) : lance les conteneurs en arrière-plan

À ce stade :
- MinIO est en cours d’exécution
- le conteneur Python est prêt

3. Accès à l’interface MinIO

Ouvrir le navigateur :

```
http://localhost:9001
```

Puis se connecter avec :

- **Login** : `minioadmin`  
- **Password** : `minioadmin`

:warning: À ce moment-là, **aucun bucket n’existe encore**, car il sera créé automatiquement par le script Python.

4. Ingestion des données (Couche Bronze)

```bash
docker compose run python python fetch_ademe_to_minio.py
```

Ce script va :
- appeler l’API ADEME
- créer le bucket `ademe-data` si besoin
- stocker les fichiers JSON dans le dossier logique :

```
raw/
```

5. Transformation en CSV (Couche Silver)

```bash
docker compose run python python json_to_csv.py
```

Ce script va :
- lire tous les fichiers JSON depuis MinIO
- les regrouper dans un seul DataFrame
- créer un fichier CSV

Stocké dans :

```
processed/ademe_all.csv
```

6. Conversion en Parquet (Couche Gold)

```bash
docker compose run python python csv_to_parquet.py
```

Ce script va :
- convertir les données en format Parquet
- appliquer un **partitionnement** par `etiquette_dpe`
- uploader les fichiers dans :

```
final/parquet/
```

### Résultat  attendu

Dans MinIO, nous verrons :

```
ademe-data/
 ├── raw/            (JSON bruts → Bronze)
 ├── processed/      (CSV → Silver)
 └── final/
     └── parquet/    (Parquet partitionné → Gold)
```

### Tester la persistance des données MinIO avec les volumes Docker

1. Arrête et supprime uniquement les **conteneurs** :

```bash
docker compose down
```

À ce stade :
- les conteneurs sont supprimés
- le volume `minio_data` est toujours présent
Redémarrer l’environnement

2. Relance les services :

```bash
docker compose up -d
```

3. Puis retourne sur :

```
http://localhost:9001
```

**Conclusion :**  
Les données ne sont pas stockées dans le conteneur mais dans le **volume Docker**.


## TD : Gestion des utilisateurs et policies

Dans ce TP, nous allons approfondir l’utilisation de MinIO en créant des utilisateurs, en leur assignant des policies (droits d’accès) et en testant la connexion depuis Python.  
Nous réutilisons le TP1 pour les scripts de gestion des données ADEME, mais nous ajoutons une couche de sécurité et de contrôle d’accès.

### Arborescence du projet

```
tp-minio-ademe/
├── docker-compose.yml
├── python/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── test_users.py
│   ├── fetch_ademe_to_minio.py
│   ├── json_to_csv.py
│   └── csv_to_parquet.py
├── ubuntu/
│   └── Dockerfile
├── policies/
│   ├── read-policy.json
│   └── write-policy.json
```

Explications :

- `python/` : contient tous les scripts Python du TP1 et le nouveau script `test_users.py`.  
- `ubuntu/` : contiendra un Dockerfile pour un conteneur Ubuntu temporaire utilisé pour configurer `mc`, créer les utilisateurs et les policies.  
- `policies/` : fichiers JSON décrivant les droits en lecture et écriture pour MinIO.

MinIO fournit un client officiel appelé **`mc` (MinIO Client)** qui permet de :  
- Créer des alias pour se connecter à un serveur MinIO.  
- Ajouter et gérer des utilisateurs.  
- Créer et attacher des policies (droits en lecture/écriture) à ces utilisateurs.  

:warning: Le serveur MinIO ne contient que le serveur et la console web, il ne permet pas de créer des utilisateurs ni des policies.  Le conteneur Ubuntu sert donc de **station d’administration portable**, compatible sur tous les systèmes et réutilisable.

### ubuntu/Dockerfile

```dockerfile
FROM ubuntu:22.04

# Installer wget pour télécharger mc (MinIO client)
RUN apt-get update && apt-get install -y wget curl && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /mc

# Télécharger et rendre exécutable le client mc
RUN wget https://dl.min.io/client/mc/release/linux-amd64/mc && chmod +x mc

ENV PATH="/mc:${PATH}"
```

Ce conteneur Ubuntu est utilisé pour exécuter le client `mc` et configurer MinIO : alias, création d’utilisateurs et attribution des policies.


### policies/read-policy.json

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:GetBucketLocation",
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::*"]
    }
  ]
}
```

### policies/write-policy.json

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:GetBucketLocation",
        "s3:ListBucket",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::*"]
    }
  ]
}
```

Explication :
- `read-policy` : accès en lecture seule (liste des buckets, lecture des objets).  
- `write-policy` : lecture/écriture et suppression des objets.  


### python/test_users.py

```python
import boto3

MINIO_ENDPOINT = "http://minio:9000"

users = {
    "etl-user": "etlpassword",
    "reader-user": "readerpassword"
}

# Test de connexion pour chaque utilisateur et récupération des buckets
for user, pwd in users.items():
    print(f"Test connexion pour {user}...")
    try:
        s3 = boto3.client(
            "s3",
            endpoint_url=MINIO_ENDPOINT,
            aws_access_key_id=user,
            aws_secret_access_key=pwd
        )
        buckets = s3.list_buckets()
        print(f"✅ {user} connecté. Buckets: {[b['Name'] for b in buckets['Buckets']]}")
    except Exception as e:
        print(f"❌ Erreur pour {user}: {e}")
```

Explication :
Ce script permet aux étudiants de vérifier que les utilisateurs et leurs droits fonctionnent correctement depuis Python, grâce à la compatibilité S3 de MinIO.


### Commandes à exécuter


1. Build du conteneur Ubuntu pour mc

```bash
docker build -t tp2-ubuntu ./ubuntu
```

2. Vérifier le réseau Docker du TP2

```bash
docker network ls
```

Repérer le réseau par défaut créé par `docker-compose`. Par exemple : `tp-minio-ademe_default`.

3. Lancer un conteneur Ubuntu temporaire pour créer les users et policies

```bash
docker run -it --network tp-minio-ademe_default -v ${PWD}/policies:/policies tp2-ubuntu
```

Puis, à l’intérieur du conteneur Ubuntu :

```bash
mc alias set local http://minio:9000 minioadmin minioadmin

mc admin user add local etl-user etlpassword
mc admin user add local reader-user readerpassword

mc admin policy create local write-policy /policies/write-policy.json
mc admin policy create local read-policy /policies/read-policy.json

mc admin policy attach local write-policy --user etl-user
mc admin policy attach local read-policy --user reader-user
```

Ensuite quitter le conteneur avec `exit`.

4. Lancer le script Python de test des utilisateurs

:bulb: Pas besoin de rebuild le conteneur Python si aucun package ou Dockerfile n’a été modifié, car le volume monté contient déjà tous les scripts.  

```bash
docker compose run python python test_users.py
```

:bulb: Vérifie que chaque utilisateur peut se connecter et que ses droits (lecture/écriture) sont appliqués correctement.


5. Tester les policies directement dans l'interface MinIO.


6. Modifier les scripts python du TP1 en utilisant les accès du user `etl-user`



## TD : MinIO et Apache Iceberg

Dans ce TP, vous allez :

- Comprendre comment **Spark interagit avec un Data Lake**
- Lire des données depuis MinIO via le protocole **S3A** (S3A est le connecteur Hadoop/Spark qui permet d’accéder à MinIO ou Amazon S3 comme un système de fichiers.)
- Transformer des fichiers **Parquet** dans une **table Iceberg**

Ce TP vous permet de simuler une architecture **Lakehouse** comme en entreprise.

### Arborescence du projet

```bash
tp-minio-ademe/
├── docker-compose.yml
├── python/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── test_users.py
│   ├── fetch_ademe_to_minio.py
│   ├── json_to_csv.py
│   └── csv_to_parquet.py
├── ubuntu/
│   └── Dockerfile
├── policies/
│   ├── read-policy.json
│   └── write-policy.json
└── spark/
    ├── Dockerfile
    ├── spark-defaults.conf
    └── jobs/
        └── job_parquet_to_iceberg.py
```


### Configuration Spark

1. `spark/spark-defaults.conf`

Ce fichier configure Spark pour :

- Charger les **JARs nécessaires**
- Connecter Spark à MinIO via **S3A**
- Activer le **catalogue Iceberg**

```properties
spark.jars=/opt/jars/iceberg-spark-runtime-3.5_2.12-1.6.0.jar,\
/opt/jars/hadoop-aws-3.3.4.jar,\
/opt/jars/aws-java-sdk-bundle-1.12.700.jar

# Déclaration du catalogue Iceberg
spark.sql.catalog.iceberg=org.apache.iceberg.spark.SparkCatalog
spark.sql.catalog.iceberg.type=hadoop
spark.sql.catalog.iceberg.warehouse=s3a://ademe-data/

# Connexion à MinIO
spark.hadoop.fs.s3a.endpoint=http://minio:9000
spark.hadoop.fs.s3a.access.key=minioadmin
spark.hadoop.fs.s3a.secret.key=minioadmin
spark.hadoop.fs.s3a.path.style.access=true

# Activation Iceberg
spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions
```

💡 **Pourquoi c’est important ?**  
Sans ces paramètres, Spark ne peut pas lire/écrire dans MinIO ni utiliser Iceberg.

2. `spark/Dockerfile`

Ce Dockerfile :

- Utilise une image Spark officielle
- Télécharge dynamiquement les dépendances
- Ajoute la configuration Spark

```dockerfile
FROM apache/spark:3.5.0

USER root
RUN mkdir -p /opt/jars

# Dépendances nécessaires
ADD https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-3.5_2.12/1.6.0/iceberg-spark-runtime-3.5_2.12-1.6.0.jar /opt/jars/
ADD https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/3.3.4/hadoop-aws-3.3.4.jar /opt/jars/
ADD https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/1.12.700/aws-java-sdk-bundle-1.12.700.jar /opt/jars/

# Configuration Spark
COPY spark-defaults.conf /opt/spark/conf/spark-defaults.conf

# Jobs Spark
COPY jobs /jobs
```

**Pourquoi ces JARs ?**  
Ils permettent à Spark de :
- Parler le protocole S3 (`hadoop-aws`)
- Comprendre Iceberg

3. `spark/jobs/job_parquet_to_iceberg.py`

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ParquetToIceberg").getOrCreate()

print("Lecture des fichiers Parquet depuis MinIO...")
df = spark.read.parquet("s3a://ademe-data/final/parquet/")

print("Aperçu des données")
df.show(5)
df.printSchema()

print("Écriture des données au format Iceberg...")
df.writeTo("iceberg.gold.dpe_table").createOrReplace()

print("Job terminé ✅")
```

**Explication** :
- `spark.read.parquet()` lit directement les fichiers depuis MinIO
- `writeTo()` crée une table Iceberg logique
- Il n’y a **aucun stockage local**, tout passe par S3A

4. Service Spark dans docker-compose

À ajouter dans ton `docker-compose.yml` :

```yaml
  spark:
    build: ./spark
    container_name: spark
    depends_on:
      - minio
    volumes:
      - ./spark/jobs:/jobs
```

**Explication** :
- `build: ./spark` → construit l’image Docker depuis ton dossier `spark/`
- `depends_on` → démarre MinIO avant Spark
- `volumes` → permet de modifier les jobs sans reconstruire l’image

---

### Commandes Docker expliquées

1. Construire les images

```bash
docker compose build
```

Cette commande :
- Lit les `Dockerfile`
- Télécharge Spark + dépendances
- Prépare toutes les images


2. Démarrer les services

```bash
docker compose up -d
```

Démarre :
- MinIO
- Spark
En arrière-plan (`-d` = detached).

3. Lister les conteneurs actifs

```bash
docker ps
```

Vérifie que :
- `minio`
- `spark`
fonctionnent correctement.

4. Lancer le job Spark

```bash
docker compose run spark /opt/spark/bin/spark-submit /jobs/job_parquet_to_iceberg.py
```

Cette commande :
- Lance un conteneur Spark temporaire
- Exécute ton job Python
- Se connecte à MinIO
- Lit Parquet → Écrit Iceberg

5. Résultat attendu

Si tout fonctionne :

- Spark affiche un aperçu des données
- La table Iceberg est créée
- Les métadonnées apparaissent dans MinIO
- Le job se termine par :  
  `Job terminé ✅`

# Cloud Computing

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/9/93/Nuage33.png" alt="Source de l'image" width="600"/>
</p>

- [Cloud Computing](#cloud-computing)
- [Projet Cloud Computing : Pipeline Data avec Docker](#projet-cloud-computing--pipeline-data-avec-docker)
  - [Objectifs pédagogiques](#objectifs-pédagogiques)
  - [Données utilisées](#données-utilisées)
  - [Architecture globale du projet](#architecture-globale-du-projet)
  - [Description des composants et intérêt dans le projet](#description-des-composants-et-intérêt-dans-le-projet)
  - [Flux des données](#flux-des-données)
  - [Mise en place avec Docker](#mise-en-place-avec-docker)
  - [Conclusion](#conclusion)
- [Programme pédagogique du TP Cloud Computing](#programme-pédagogique-du-tp-cloud-computing)
  - [1 : Concepts API avec Python](#1--concepts-api-avec-python)
  - [2 : Docker](#2--docker)
  - [3 : Kafka et PostgreSQL](#3--kafka-et-postgresql)
  - [4 : Spark / Iceberg / MinIO](#4--spark--iceberg--minio)
  - [5 : Trino / Superset](#5--trino--superset)
  - [Bonus : Airflow (OPTION)](#bonus--airflow-option)
  - [Progression pédagogique](#progression-pédagogique)
- [Compétences à mettre dans son CV après le module](#compétences-à-mettre-dans-son-cv-après-le-module)
- [Cahier des charges (en cours de rédaction)](#cahier-des-charges-en-cours-de-rédaction)

# Projet Cloud Computing : Pipeline Data avec Docker

## Objectifs pédagogiques

- Découvrir les bases de **l’architecture cloud moderne** et des pipelines de données.  
- Comprendre **le rôle de chaque brique** dans un projet de données : ingestion, persistance, transformation, stockage analytique, exploration et visualisation.  
- Manipuler **les flux de données** pour se familiariser avec le streaming.  
- Découvrir la **containerisation** avec Docker et l’orchestration.  
- Comprendre le fonctionnement d’un cluster distribué (Cluster manager, workers, driver) grâce à Spark/Trino.

## Données utilisées

- **Source** : Les données DPE de l'[API ADEME](https://data.ademe.fr/datasets?topics=BR8GjsXga).
- **Exemple de champs** : numéro DPE, date réception, code postal, étiquette énergétique.  
- **Filtre utilisé dans le TP** : données d'un département ou d'une région.  

**Objectif :** Ce projet TP permet de découvrir les concepts fondamentaux du cloud computing et du traitement de données en flux et en batch, en manipulant des technologies modernes telles que Kafka, PostgreSQL, Spark, Iceberg, MinIO, Trino, Airflow, Superset et Flask.  
Les données utilisées proviennent de l’API ADEME sur les **DPE (Diagnostics de Performance Énergétique)**.

## Architecture globale du projet

Le pipeline se découpe en plusieurs briques, chacune jouant un rôle précis :

````markdown
                ┌─────────────┐
                │   Flask     │
                │ (API / Web) │
                └─────┬───────┘
                      │ Publie / reçoit des données DPE
                      ▼
                ┌─────────────┐
                │   Kafka     │
                │ (Topics &   │
                │ Partitions) │
                └─────┬───────┘
                      │ Messages streams
                      ▼
        ┌───────────────────┐
        │  Consumers Python │
        │   / PostgreSQL    │
        └─────────┬─────────┘
                  │ Données persistées
                  ▼
         ┌─────────────┐       ┌───────────────┐
         │ PostgreSQL  │       │    Spark      │
         │   DB        │──────▶│ / PySpark     │
         │ (brutes)    │       │ Transformation│
         └─────────────┘       └─────┬─────────┘
                                    │ Écriture des données traitées
                                    ▼
                              ┌───────────────┐
                              │ Apache Iceberg│
                              │  (Tables dans │
                              │    MinIO)     │
                              └─────┬─────────┘
                                    │ Lecture analytique SQL
                                    ▼
                            ┌───────────────┐
                            │     Trino     │
                            │  (SQL Engine) │
                            └─────┬─────────┘
                                    │ Fournit des vues / queries
                                    ▼
                            ┌───────────────┐
                            │   Superset    │
                            │ Dashboard BI  │
                            └───────────────┘
````

Bonus Airflow pour l'orchestration et le scheduling (OPTION)

## Description des composants et intérêt dans le projet

| Composant | Rôle dans le pipeline | Intérêt pédagogique / Cloud |
|-----------|--------------------|----------------------------|
| **Flask** | API pour récupérer les données DPE depuis l’API ADEME et les publier dans Kafka | Découvrir les API REST, encapsuler la logique dans un conteneur, interaction avec le cloud |
| **Kafka** | Message broker pour ingestion asynchrone et streaming | Comprendre la communication entre services, le streaming de données, le découplage producteur/consommateur |
| **Consumers Python** | Consomment les messages Kafka et les stockent dans PostgreSQL | Illustrer la consommation de flux, le traitement côté client et la persistance dans un stockage relationnel |
| **PostgreSQL** | Stockage des données brutes ou pré-traitées | Découvrir le stockage relationnel dans un conteneur, persistance, isolation et backup |
| **Spark / PySpark** | Traitement et transformation massive des données | Découvrir le traitement distribué, les transformations batch / micro-batch et l’enrichissement des données |
| **Apache Iceberg + MinIO** | Stockage analytique optimisé en colonnes, compatible SQL | Comprendre le stockage objet (S3/MinIO), OLAP, tables versionnées et persistance pour analytics |
| **Trino** | Moteur SQL distribué pour lire plusieurs sources (PostgreSQL, Iceberg) | Découvrir le SQL sur données distribuées et hétérogènes, exploration et BI |
| **Superset** | Tableau de bord BI pour visualiser les données | Découvrir la visualisation de données, reporting et analyses graphiques |
| **Airflow** | Orchestration des pipelines et scheduling | Comprendre l’automatisation, la planification et la gestion de workflows dans le cloud |

## Flux des données

1. **Ingestion** : Les données DPE sont récupérées depuis l’API ADEME par Flask et publiées dans Kafka.  
2. **Persistance initiale** : Les consumers Python lisent les messages Kafka et les stockent dans PostgreSQL.  
3. **Traitement analytique** : Spark transforme les données depuis PostgreSQL et écrit les résultats dans Iceberg sur MinIO.  
4. **Exploration SQL / BI** : Trino interroge Iceberg (et PostgreSQL si besoin) pour fournir des vues à Superset.  
5. **Orchestration** : Airflow gère l’exécution de tous les composants selon un DAG défini, avec gestion des dépendances et scheduling.  

## Mise en place avec Docker

- Chaque composant est conteneurisé avec **Docker**.  
- Le projet inclut un **docker-compose.yml** pour lancer l’ensemble des services en une seule commande.  
- Avantages pédagogiques :  
  - Isolation des composants  
  - Reproductibilité sur n’importe quelle machine  
  - Découverte des volumes Docker pour persister les données (PostgreSQL, MinIO)  

## Conclusion

Ce projet est une **introduction pratique au cloud computing et au traitement de données modernes**.  
Il illustre comment orchestrer plusieurs composants conteneurisés pour :

- l’ingestion en streaming (Kafka)  
- le stockage relationnel (PostgreSQL)  
- le traitement distribué (Spark + Iceberg)  
- l’interrogation SQL distribuée (Trino)  
- la visualisation BI (Superset)  
- et l’orchestration des workflows (Airflow)  

Les étudiants découvrent ainsi **les concepts clés du cloud, de la conteneurisation, de la data engineering et de la BI** en un seul projet pratique.


# Programme pédagogique du TP Cloud Computing

Ce projet est conçu pour être réalisé **progressivement après 20H de cours**, chacun apportant une brique technique essentielle.  
Les étudiants découvrent les concepts et les outils **pas à pas**, avant de mettre en place le pipeline complet.

## 1 : Concepts API avec Python

- **Objectif** : Comprendre comment consommer une API REST avec Python.  
- **Application pratique** : Accéder à l’API ADEME DPE, récupérer des données filtrées (ex. département 69, année 2024).  
- **Compétences acquises** :  
  - Utiliser `requests` et manipuler le JSON  
  - Structurer les données pour ingestion future  
  - Tester la récupération des données avant de les stocker  

## 2 : Docker

- **Objectif** : Découvrir la conteneurisation avec Docker et isoler les applications.  
- **Application pratique** :  
  - Conteneuriser le script Python qui consomme l’API ADEME  
  - Créer des volumes pour persister les données  
  - Lancer plusieurs conteneurs avec `docker-compose`  
- **Compétences acquises** :  
  - Construire et exécuter des conteneurs  
  - Gérer les images et volumes Docker  
  - Comprendre l’isolation des applications dans un environnement cloud-like  

## 3 : Kafka et PostgreSQL

- **Objectif** : Comprendre le streaming et la persistance des données.  
- **Application pratique** :  
  - Publier les données ADEME dans Kafka via un **Producer Python**  
  - Consommer les messages avec un **Consumer Python** et stocker dans **PostgreSQL**  
- **Compétences acquises** :  
  - Concepts de **topic, partition, offset, consumer group**  
  - Lecture/écriture de données en flux  
  - Persistance relationnelle et isolation des données  

## 4 : Spark / Iceberg / MinIO

- **Objectif** : Découvrir le traitement distribué et le stockage analytique.  
- **Application pratique** :  
  - Lire les données de PostgreSQL via Spark  
  - Effectuer des transformations / nettoyage  
  - Stocker les données dans **Iceberg sur MinIO** pour analytics  
- **Compétences acquises** :  
  - Comprendre le fonctionnement d’un cluster Spark (driver, workers, cluster manager)  
  - Stockage objet et colonnes optimisées pour OLAP  
  - Flux batch et micro-batch de données  

## 5 : Trino / Superset

- **Objectif** : Découvrir l’interrogation SQL distribuée et la visualisation BI.  
- **Application pratique** :  
  - Utiliser Trino pour interroger Iceberg et PostgreSQL simultanément  
  - Créer des dashboards avec Superset  
- **Compétences acquises** :  
  - Fonctionnement d’un moteur SQL distribué (Coordinator et workers)  
  - Exploration de données hétérogènes  
  - Création de tableaux de bord interactifs  


## Bonus : Airflow (OPTION)

- **Objectif** : Découvrir l’orchestration et l’automatisation des pipelines de données.  
- **Application pratique** :  
  - Définir un DAG orchestrant Flask → Kafka → Consumers → Spark → Iceberg → Trino → Superset  
- **Compétences acquises** :  
  - Planification et gestion de workflows  
  - Dépendances entre tâches  
  - Automatisation de pipelines cloud-like  

## Progression pédagogique

1. **API Python** : comprendre les données  
2. **Docker** : conteneurisation et isolation  
3. **Kafka + PostgreSQL** : ingestion et persistance  
4. **Spark / Iceberg / MinIO** : traitement distribué et stockage analytique  
5. **Trino / Superset** : interrogation distribuée et visualisation  
6. **Airflow (bonus)** : orchestration des pipelines  

Cette progression permet aux étudiants de **construire progressivement un pipeline complet de cloud computing** avec des outils modernes.


# Compétences à mettre dans son CV après le module

| Profil / Métier            | Compétences clés développées | Exemples concrets dans le projet |
|----------------------------|-----------------------------|---------------------------------|
| **Data Engineer**          | - Ingestion Kafka <br> - Consumers Python <br> - Stockage PostgreSQL <br> - Traitement Spark <br> - Stockage analytique Iceberg <br> - Conteneurisation Docker <br> - Compréhension cluster distribué Spark | - Producer / Consumer pour API ADEME <br> - PostgreSQL pour persistance <br> - Spark transforme les données <br> - Iceberg sur MinIO <br> - Docker-compose pour tout le pipeline |
| **Data Analyst / Scientist** | - SQL distribué (Trino) <br> - Visualisation BI (Superset) <br> - Exploration et analyse des données | - Trino interrogeant Iceberg / PostgreSQL <br> - Dashboards Superset sur DPE ADEME |
| **Cybersécurité (niveau TP / minimal)** | - Authentification API (token) <br> - Isolation réseau Docker <br> - Volumes pour persistance sécurisée <br> - Accès limité aux services <br> - Bonnes pratiques sur images et ports exposés | - Flask API avec clé API simple <br> - Réseau interne Docker pour Kafka/PostgreSQL <br> - Volumes PostgreSQL / MinIO <br> - Mots de passe pour Superset / Trino |
| **Compétences transverses** | - Manipulation API JSON <br> - Pipeline complet ingestion → stockage → transformation → visualisation <br> - Compréhension cluster distribué Spark / Trino | - Mise en place du pipeline complet sur Docker <br> - Découverte des concepts de cluster et partitionnement <br> - Kafka / PostgreSQL / Iceberg / Superset intégrés |


# Cahier des charges (en cours de rédaction)

| Composant          | Mesures de sécurisation pédagogiques | Objectif pédagogique |
|-------------------|------------------------------------|--------------------|
| PostgreSQL         | - Mot de passe pour l’utilisateur <br> - Port limité au réseau interne Docker <br> - Volumes pour persistance des données | - Comprendre la sécurité de la base <br> - Sensibiliser à l’isolation et à la persistance sécurisée |
| Kafka              | - Réseau interne Docker pour les conteneurs <br> - Accès restreint aux topics <br> - Versions fixes d’images officielles | - Comprendre le contrôle d’accès et l’isolation des brokers <br> - Réduire les risques liés aux vulnérabilités |
| Flask API          | - Token / clé API simple (`X-API-KEY`) <br> - Vérification du token avant traitement | - Découvrir l’authentification basique pour les API <br> - Sensibiliser à l’accès restreint |
| Docker & Volumes   | - Volumes pour PostgreSQL et MinIO <br> - Isolation des conteneurs | - Comprendre la persistance des données et l’isolation dans Docker |
| Superset / Trino   | - Mot de passe admin simple <br> - Restrictions d’accès aux dashboards / SQL editor | - Comprendre l’authentification pour BI et moteur SQL distribué |
| Bonnes pratiques   | - Fixer les versions des images <br> - Sensibiliser aux ports exposés et volumes non sécurisés | - Découvrir les bonnes pratiques de sécurité sur les conteneurs et le cloud |

:bulb: Cette sécurisation est volontairement simple et pédagogique, adaptée à un TP de 20h. Elle **introduit les concepts clés de cybersécurité** : authentification, isolation, contrôle d’accès et bonnes pratiques sur les conteneurs.
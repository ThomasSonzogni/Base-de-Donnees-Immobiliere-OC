# Créer et utiliser une base de données immobilières - Projet DATAImmo 🏡

# Description du projet 📝

Le projet DATAImmo a pour objectif de créer et d'utiliser une base de données immobilières afin de mieux prévoir le prix de vente des biens immobiliers. Ce projet s'inscrit dans une initiative stratégique de Laplace Immo, un réseau national d’agences immobilières, visant à se démarquer de la concurrence grâce à l’analyse des données du marché.

Le projet consiste à modifier et optimiser une base de données utilisée pour collecter les transactions immobilières et foncières en France 🇫🇷. Une fois la base de données améliorée, elle sera utilisée pour analyser le marché immobilier, fournir des insights sur les tendances du marché et aider les agences régionales à mieux accompagner leurs clients dans leurs achats et ventes de biens immobiliers.

# Objectifs principaux 🎯

Réarranger les données pour construire un dictionnaire de données complet et un schéma relationnel conforme aux normes de la 3NF (Troisième Forme Normale).

Créer une base de données performante ⚙️, avec une structure optimisée pour l’exécution de requêtes complexes.

Effectuer des requêtes SQL 📑 afin d’extraire des données clés permettant d’analyser le marché immobilier, comme les prix de vente 💰, les surfaces 📏, et les tendances régionales 🌍.

Respect du RGPD 📜

Le projet respecte pleinement le Règlement Général sur la Protection des Données (RGPD), garantissant que toutes les données personnelles et sensibles sont manipulées de manière sécurisée 🔒 et respectueuse des droits des utilisateurs.

# Outils utilisés 🛠️

SQLite 🗄️ : Gestion de la base de données relationnelle.

Excel 📊 : Élaboration du dictionnaire de données et analyse préliminaire des données.

Draw.io 🎨 : Création du schéma relationnel de la base de données.


# Principaux Résultats :
Résultats du premier semestre 2020 📅

Région parisienne 🏙️ : Enregistre le plus grand nombre de ventes immobilières en France.

Augmentation des ventes 📈 : Une hausse des ventes entre le premier et le second trimestre de 2020 a été observée.

Surface vs Prix au m² 📏💵 : Plus la surface des biens augmente, plus le prix au m² tend à diminuer.

Centre-Val de Loire 🌳 : Cette région possède la plus grande superficie de terrain parmi les biens immobiliers vendus.

# Structure du projet 📂

La structure du projet est organisée de manière à faciliter l’accès aux différentes ressources et scripts :

BASE_DE_DONNÉE_IMMOBILIERE/
│── data/                      # Données sources en format CSV
│   │── biens.csv              # Liste des biens immobiliers
│   │── Communes.csv           # Informations sur les communes
│   │── departement.csv        # Informations sur les départements
│   │── donnees_de_base.zip    # Archive contenant les données de base
│   │── région.csv             # Informations sur les régions
│   │── ventes.csv             # Historique des ventes immobilières
│
│── documentation_bdd/         # Documentation sur la base de données
│   │── bdd_sql/               # Dossier avec la base SQL
│   │── Dictionnaire_de_donnees.xlsx  # Dictionnaire des données
│   │── schema_relationnel_base_de_donnee_immo.png  # Schéma relationnel de la BDD
│
│── presentation/              # Présentation du projet
│   │── support_presentation_requete_base_immo.pptx  # Présentation PowerPoint
│
│── requetes/                  # Scripts SQL
│   │── creation_bdd.sql       # Script de création de la base de données
│   │── requetes_bdd.sql       # Requêtes SQL pour l'exploitation des données
│
│── .gitignore                 # Fichier pour ignorer certains fichiers dans Git
│── README.md                  # Documentation principale du projet


# Le dictionnaire de données

<img width="1025" height="438" alt="image" src="https://github.com/user-attachments/assets/1b010cb9-ef16-4e4a-9516-9eb921ee833b" />

# Schéma relationnel modifié


<img width="844" height="458" alt="image" src="https://github.com/user-attachments/assets/608ce4bf-d743-4a9d-9ab0-47173fcdb729" />


# Code SQL générant les tables 
 

<img width="945" height="789" alt="image" src="https://github.com/user-attachments/assets/251b169c-a08e-4bb5-82bc-170b0fb11d4f" />


# Capture d’écran base de données chargée

<img width="823" height="320" alt="image" src="https://github.com/user-attachments/assets/6ee93854-8d9a-4326-bca6-9dd24166276d" />

<img width="891" height="38" alt="image" src="https://github.com/user-attachments/assets/e7605f33-3992-45df-8304-9609887163b1" />

<img width="945" height="32" alt="image" src="https://github.com/user-attachments/assets/a05c2ca1-8001-41d4-a5ee-9ded0a93391a" />


# Les requêtes SQL


<img width="871" height="408" alt="image" src="https://github.com/user-attachments/assets/e7789694-e295-4f0e-a1d5-298b0a26ac40" />

<img width="775" height="413" alt="image" src="https://github.com/user-attachments/assets/45545f49-35b7-4f48-9a72-bd81ba2db3d4" />

<img width="787" height="867" alt="image" src="https://github.com/user-attachments/assets/2f8ec71c-3212-4308-afab-a557a372ce1a" />

<img width="765" height="371" alt="image" src="https://github.com/user-attachments/assets/a89a303d-ccaf-4260-ac99-907f8c4e158b" />

<img width="760" height="226" alt="image" src="https://github.com/user-attachments/assets/c90f3d1a-da7c-4718-8a91-69adbf5e87c3" />

<img width="772" height="353" alt="image" src="https://github.com/user-attachments/assets/63088516-6d6c-4a74-a435-35660a49f167" />

<img width="803" height="298" alt="image" src="https://github.com/user-attachments/assets/2ad223c8-a696-492c-a0cb-c5b3d7e21edb" />

<img width="819" height="369" alt="image" src="https://github.com/user-attachments/assets/1a7f3799-7522-4384-9bb7-3bfbcb5ca94f" />

<img width="807" height="277" alt="image" src="https://github.com/user-attachments/assets/a4e8ec1b-dd05-4e2c-bda1-d1fa9883b7c2" />

<img width="836" height="619" alt="image" src="https://github.com/user-attachments/assets/fe0957be-fbd4-40c7-9974-50ce88ec6aff" />

<img width="634" height="731" alt="image" src="https://github.com/user-attachments/assets/c0505c17-c5d6-4426-a4f1-f73d158c503a" />

<img width="568" height="605" alt="image" src="https://github.com/user-attachments/assets/b881aa6d-8628-4aa6-ba3a-9144167e1047" />


































































































































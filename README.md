# DATAImmo — Base de données immobilières

## Description

Le projet **DATAImmo** a pour objectif de créer et d'exploiter une base de données
immobilières afin de mieux prévoir le prix de vente des biens. Il s'inscrit dans une
initiative stratégique de **Laplace Immo**, un réseau national d'agences immobilières,
visant à se démarquer de la concurrence grâce à l'analyse des données du marché.

Le projet consiste à restructurer et optimiser une base de données collectant les
transactions immobilières et foncières en France. Une fois améliorée, elle permet
d'analyser le marché, d'identifier les tendances et d'aider les agences régionales
à mieux accompagner leurs clients.

---

## Objectifs

- Construire un **dictionnaire de données** complet et un schéma relationnel conforme à la **3NF** (Troisième Forme Normale).
- Créer une base de données performante, optimisée pour des requêtes complexes.
- Rédiger des **requêtes SQL** pour extraire des indicateurs clés : prix de vente, surfaces, tendances régionales.
- Garantir le respect du **RGPD** dans la manipulation des données personnelles.

---

## Outils utilisés

| Outil   | Usage                                           |
|---------|-------------------------------------------------|
| SQLite  | Gestion de la base de données relationnelle     |
| Excel   | Dictionnaire de données et analyse préliminaire |
| Draw.io | Création du schéma relationnel                  |

---

## Résultats — Premier semestre 2020

- **Région parisienne** : plus grand nombre de ventes immobilières en France.
- **Évolution trimestrielle** : hausse des ventes entre le T1 et le T2 2020.
- **Surface / Prix au m²** : plus la surface augmente, plus le prix au m² tend à baisser.
- **Centre-Val de Loire** : région avec la plus grande superficie de terrain vendue.

---

## Structure du projet

```
DATAImmo/
├── data/                               # Données sources (CSV)
│   ├── biens.csv
│   ├── Communes.csv
│   ├── departement.csv
│   ├── donnees_de_base.zip
│   ├── région.csv
│   └── ventes.csv
│
├── documentation_bdd/                  # Documentation de la base
│   ├── bdd_sql/
│   ├── Dictionnaire_de_donnees.xlsx
│   └── schema_relationnel_base_de_donnee_immo.png
│
├── presentation/
│   └── support_presentation_requete_base_immo.pptx
│
├── requetes/                           # Scripts SQL
│   ├── creation_bdd.sql
│   └── requetes_bdd.sql
│
├── .gitignore
└── README.md
```

---

## Schéma relationnel

<img width="652" height="718" alt="Schéma relationnel normalisé 3NF" src="https://github.com/user-attachments/assets/5fe03cf5-930c-4fac-a0a8-794a0bf3c1a4" />

---

## Création des tables — SQLite

<img width="717" height="513" alt="Script SQL de création des tables" src="https://github.com/user-attachments/assets/0bce08f4-32ff-4fb5-be5a-3665645d6681" />
<img width="637" height="483" alt="Script SQL de création des tables (suite)" src="https://github.com/user-attachments/assets/aaa5d80f-3d8e-44fe-9458-905829e75fbf" />

---

## Requêtes SQL

### Requête 1 — Nombre total d'appartements vendus au 1er semestre 2020

```sql
SELECT COUNT(v.Id_ventes) AS nombre_appartements_vendus
FROM Ventes v
JOIN Biens b ON v.Id_refbien = b.Id_bien
WHERE v.Date_mutation BETWEEN '2020-01-01' AND '2020-06-30'
  AND b.Type_local = 'Appartement';
```

---

### Requête 2 — Nombre de ventes d'appartements par région au 1er semestre 2020

```sql
SELECT r.region_code,
       r.reg_nom AS nom_region,
       COUNT(v.Id_ventes) AS nombre_ventes
FROM Ventes v
JOIN Biens b ON v.Id_refbien = b.Id_bien
JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
WHERE v.Date_mutation BETWEEN '2020-01-01' AND '2020-06-30'
  AND b.Type_local = 'Appartement'
GROUP BY r.region_code
ORDER BY nombre_ventes DESC;
```

---

### Requête 3 — Proportion des ventes d'appartements par nombre de pièces

```sql
WITH ventes_appartements AS (
    SELECT b.nb_piece_principale AS nb_pieces,
           COUNT(*) AS nb_ventes_par_pieces
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Type_local = 'Appartement'
    GROUP BY b.nb_piece_principale
),
total_ventes_appartements AS (
    SELECT COUNT(*) AS total_ventes
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Type_local = 'Appartement'
)
SELECT va.nb_pieces AS nombre_pieces,
       va.nb_ventes_par_pieces,
       ROUND(CAST(va.nb_ventes_par_pieces AS FLOAT) / tva.total_ventes, 4) AS proportion
FROM ventes_appartements va
JOIN total_ventes_appartements tva
ORDER BY va.nb_pieces;
```

---

### Requête 4 — Top 10 des départements avec le prix au m² le plus élevé

```sql
WITH prix_m2 AS (
    SELECT b.CodeComm_CodeDep AS code_comm,
           ROUND(CAST(v.Valeur_fonciere AS FLOAT) / b.Surface_Carrez_du_1er_Lot, 4) AS prix_m2
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Surface_Carrez_du_1er_Lot > 0
)
SELECT d.dep_nom_num,
       AVG(pm.prix_m2) AS prix_moyen_m2
FROM prix_m2 pm
JOIN Communes c ON pm.code_comm = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
GROUP BY d.dep_nom_num
ORDER BY prix_moyen_m2 DESC
LIMIT 10;
```

---

### Requête 5 — Prix moyen du m² d'une maison en Île-de-France

```sql
WITH prix_m2_maison AS (
    SELECT b.CodeComm_CodeDep AS code_comm,
           CAST(v.Valeur_fonciere AS FLOAT) / b.Surface_Carrez_du_1er_Lot AS prix_m2
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Type_local = 'Maison'
      AND v.Valeur_fonciere IS NOT NULL
      AND b.Surface_Carrez_du_1er_Lot > 0
)
SELECT r.reg_nom,
       ROUND(AVG(prix_m2), 2) AS prix_moyen_m2_maison
FROM prix_m2_maison pmm
JOIN Communes c ON pmm.code_comm = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
WHERE r.reg_nom = 'Ile-de-France'
GROUP BY r.reg_nom;
```

---

### Requête 6 — Top 10 des appartements les plus chers (avec région et surface)

```sql
SELECT b.Id_bien,
       b.Surface_Carrez_du_1er_Lot AS nb_m2,
       CAST(v.Valeur_fonciere AS FLOAT) AS valeur_fonciere,
       r.reg_nom AS nom_region
FROM Ventes v
JOIN Biens b ON v.Id_refbien = b.Id_bien
JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
WHERE b.Type_local = 'Appartement'
ORDER BY valeur_fonciere DESC
LIMIT 10;
```

---

### Requête 7 — Taux d'évolution des ventes entre le T1 et le T2 2020

```sql
WITH ventes_premier_trimestre AS (
    SELECT COUNT(v.Id_ventes) AS nb_ventes_premier_trimestre
    FROM Ventes v
    WHERE v.Date_mutation BETWEEN '2020-01-01' AND '2020-03-31'
),
ventes_second_trimestre AS (
    SELECT COUNT(v.Id_ventes) AS nb_ventes_second_trimestre
    FROM Ventes v
    WHERE v.Date_mutation BETWEEN '2020-04-01' AND '2020-06-30'
)
SELECT p.nb_ventes_premier_trimestre,
       s.nb_ventes_second_trimestre,
       ((s.nb_ventes_second_trimestre - p.nb_ventes_premier_trimestre) * 100.0
        / p.nb_ventes_premier_trimestre) AS taux_evolution
FROM ventes_premier_trimestre p,
     ventes_second_trimestre s;
```

---

### Requête 8 — Classement des régions par prix au m² pour les appartements de plus de 4 pièces

```sql
WITH prix_m2 AS (
    SELECT b.CodeComm_CodeDep AS code_comm,
           ROUND(CAST(v.Valeur_fonciere AS FLOAT) / b.Surface_Carrez_du_1er_Lot, 4) AS prix_m2
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Surface_Carrez_du_1er_Lot > 0
      AND b.nb_piece_principale > 4
      AND b.Type_local = 'Appartement'
)
SELECT r.reg_nom,
       AVG(pm.prix_m2) AS prix_moyen_m2
FROM prix_m2 pm
JOIN Communes c ON pm.code_comm = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
GROUP BY r.reg_nom
ORDER BY prix_moyen_m2 DESC;
```

---

### Requête 9 — Communes avec au moins 50 ventes au 1er trimestre 2020

```sql
SELECT c.COM AS nom_communes,
       COUNT(v.Id_ventes) AS nombre_ventes
FROM Ventes v
JOIN Biens b ON v.Id_refbien = b.Id_bien
JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
WHERE v.Date_mutation BETWEEN '2020-01-01' AND '2020-03-31'
GROUP BY c.COM
HAVING COUNT(v.Id_ventes) >= 50
ORDER BY c.COM;
```

---

### Requête 10 — Différence de prix au m² entre un appartement 2 pièces et 3 pièces

```sql
WITH valeur_fonciere_2pieces AS (
    SELECT AVG(v.Valeur_fonciere / b.Surface_Carrez_du_1er_Lot) AS prix_m2_2pieces
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Nb_piece_principale = 2
      AND b.Type_local = 'Appartement'
),
valeur_fonciere_3pieces AS (
    SELECT AVG(v.Valeur_fonciere / b.Surface_Carrez_du_1er_Lot) AS prix_m2_3pieces
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    WHERE b.Nb_piece_principale = 3
      AND b.Type_local = 'Appartement'
)
SELECT ((p3.prix_m2_3pieces - p2.prix_m2_2pieces) / p2.prix_m2_2pieces) * 100
           AS difference_en_pourcentage
FROM valeur_fonciere_2pieces p2,
     valeur_fonciere_3pieces p3;
```

---

### Requête 11 — Top 3 des communes par valeur foncière moyenne (départements 6, 13, 33, 59, 69)

```sql
WITH moyennes_valeurs_foncieres AS (
    SELECT d.dep_code AS code_departement,
           c.COM AS nom_commune,
           AVG(v.Valeur_fonciere) AS moyenne_valeur_fonciere
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
    JOIN Departements d ON c.CODDEP = d.dep_code
    WHERE d.dep_code IN (6, 13, 33, 59, 69)
    GROUP BY d.dep_code, c.COM
),
classement_top3 AS (
    SELECT code_departement,
           nom_commune,
           moyenne_valeur_fonciere,
           ROW_NUMBER() OVER (PARTITION BY code_departement ORDER BY moyenne_valeur_fonciere DESC) AS rang
    FROM moyennes_valeurs_foncieres
)
SELECT code_departement,
       nom_commune,
       moyenne_valeur_fonciere
FROM classement_top3
WHERE rang <= 3
ORDER BY code_departement, rang;
```

---

### Requête 12 — Top 20 des communes avec le plus de transactions pour 1 000 habitants (> 10 000 hab.)

```sql
SELECT c.COM AS commune,
       COUNT(v.Id_ventes) AS nb_transactions,
       (COUNT(v.Id_ventes) * 1000.0 / c.PTOT) AS transactions_pour_1000_habitants
FROM Communes c
JOIN Biens b ON c.CODEP_CODCOM = b.CodeComm_CodeDep
JOIN Ventes v ON b.Id_bien = v.Id_refbien
WHERE c.PTOT > 10000
GROUP BY c.COM, c.PTOT
ORDER BY transactions_pour_1000_habitants DESC
LIMIT 20;
```

---

### Requête optionnelle — Top 5 des communes avec la plus grande variation de prix au m² entre T1 et T2 2020

```sql
WITH prix_m2_trim1 AS (
    SELECT c.COM,
           b.CodeComm_CodeDep AS code_comm,
           ROUND(AVG(v.Valeur_fonciere / b.Surface_Carrez_du_1er_Lot), 4) AS prix_m2
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
    WHERE v.Date_mutation BETWEEN '2020-01-01' AND '2020-03-31'
      AND b.Surface_Carrez_du_1er_Lot > 0
    GROUP BY c.COM, b.CodeComm_CodeDep
),
prix_m2_trim2 AS (
    SELECT c.COM,
           b.CodeComm_CodeDep AS code_comm,
           ROUND(AVG(CAST(v.Valeur_fonciere AS FLOAT) / b.Surface_Carrez_du_1er_Lot), 4) AS prix_m2
    FROM Ventes v
    JOIN Biens b ON v.Id_refbien = b.Id_bien
    JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
    WHERE v.Date_mutation BETWEEN '2020-04-01' AND '2020-06-30'
      AND b.Surface_Carrez_du_1er_Lot > 0
    GROUP BY c.COM, b.CodeComm_CodeDep
)
SELECT p1.COM,
       p1.prix_m2 AS prix_m2_trim1,
       p2.prix_m2 AS prix_m2_trim2,
       ABS(p1.prix_m2 - p2.prix_m2) AS difference
FROM prix_m2_trim1 p1
JOIN prix_m2_trim2 p2 ON p1.code_comm = p2.code_comm
ORDER BY difference DESC
LIMIT 5;
```

---

### Requête optionnelle — Superficie moyenne des terrains de maisons par région en 2020

```sql
SELECT r.reg_nom,
       AVG(b.Surface_terrain) AS superficie_moyenne
FROM Biens b
JOIN Ventes v ON b.Id_bien = v.Id_refbien
JOIN Communes c ON b.CodeComm_CodeDep = c.CODEP_CODCOM
JOIN Departements d ON c.CODDEP = d.dep_code
JOIN Regions r ON d.reg_code = r.region_code
WHERE b.Type_local = 'Maison'
  AND v.Date_mutation BETWEEN '2020-01-01' AND '2020-12-31'
  AND b.Surface_terrain > 0
GROUP BY r.reg_nom
ORDER BY superficie_moyenne DESC;
```

---

## Auteur

**Thomas Sonzogni**



































































































































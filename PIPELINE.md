# Audit, Dictionnaire et Anonymisation de Données (Pipeline theses.fr)

## Objectifs pédagogiques

À l’issue de ce TP, l’apprenant sera capable de :

1. Connecter et interroger une API publique pour extraire des données brutes.
2. Créer et auditer une base MySQL pour y stocker les données reçues.
3. Mettre en place un audit structurel automatique à partir du schéma SQL.
4. Générer un dictionnaire de données automatisé (via `information_schema`).
5. Appliquer une anonymisation conforme RGPD / ISO/IEC 20889.
6. Produire un rapport de conformité automatisé.

---

## Contexte du projet

L’entreprise DataConsult SARL souhaite auditer et documenter un jeu de données issu de l’API publique `theses.fr`, hébergée sur [data.gouv.fr](https://www.data.gouv.fr/fr/dataservices/api-interroger-les-donnees-de-theses-fr/).

Ces données contiennent des informations sur les thèses soutenues en France : titres, auteurs, disciplines, établissements et dates de soutenance.
L’objectif est de :

* récupérer ces données ;
* les stocker dans une base MySQL ;
* générer un dictionnaire de données ;
* détecter les incohérences ;
* puis anonymiser les champs sensibles avant archivage.

---

## Prérequis techniques

* Python 3.10+
* MySQL ou MariaDB
* Bibliothèques Python :

  ```
  pip install requests mysql-connector-python
  ```
* Serveur local : Laragon / WAMP / XAMPP / Docker MySQL
* VSCode ou PyCharm comme éditeur

---

## Partie 1 — Création de l’environnement

### Étape 1 : Créer le dossier du projet

```
mkdir pipeline_theses
cd pipeline_theses
python -m venv venv
venv\Scripts\activate
pip install requests mysql-connector-python
```

### Étape 2 : Créer le fichier principal

Crée le fichier `main.py` et colle le code complet du pipeline (celui fourni précédemment, avec audit du schéma, dictionnaire, anonymisation et rapport).

---

## Partie 2 — Audit structurel (pré-dictionnaire)

### Étape 1 : Lancer le script pour créer la base

```
python main.py
```

Le script :

* crée la base `theses_data`,
* crée les tables :
  `audit_structure`, `dictionnaire_donnees`, `theses_staging`, `theses_anonymisees`,
* exécute un audit structurel.

### Étape 2 : Vérifier les tables dans MySQL

```
USE theses_data;
SHOW TABLES;
```

Résultat attendu :

```
+----------------------+
| Tables_in_theses_data |
+----------------------+
| audit_structure       |
| dictionnaire_donnees  |
| theses_anonymisees    |
| theses_staging        |
+----------------------+
```

### Étape 3 : Visualiser les anomalies détectées

```
SELECT * FROM audit_structure;
```

Exemple de résultat :

| table_name     | colonne         | anomalie     | commentaire                                      |
| -------------- | --------------- | ------------ | ------------------------------------------------ |
| theses_staging | titre           | Nullable     | Colonne sans contrainte NOT NULL                 |
| theses_staging | date_soutenance | Type suspect | Colonne texte contenant potentiellement une date |

---

## Partie 3 — Dictionnaire de Données

### Étape 1 : Générer le dictionnaire

Le script `generate_dictionnaire()` alimente la table `dictionnaire_donnees` depuis `information_schema`.

```
SELECT * FROM dictionnaire_donnees;
```

Exemple de résultat :

| table_name     | column_name | data_type | is_nullable | classification | commentaire |
| -------------- | ----------- | --------- | ----------- | -------------- | ----------- |
| theses_staging | titre       | text      | YES         | (vide)         | (vide)      |
| theses_staging | auteur      | varchar   | YES         | (vide)         | (vide)      |

### Étape 2 : Ajouter la classification manuelle

```
UPDATE dictionnaire_donnees
SET classification = CASE
    WHEN column_name IN ('auteur') THEN 'Restreinte'
    WHEN column_name LIKE '%titre%' THEN 'Publique'
    WHEN column_name LIKE '%date%' THEN 'Interne'
    ELSE 'Publique'
END;
```

---

## Partie 4 — Insertion des données brutes

### Étape 1 : Vérifier le log Python

Dans le fichier `logs/pipeline_theses.log`, on doit voir :

```
[2025-10-17 14:55:41] Appel API theses.fr avec paramètres : {'q': 'intelligence artificielle', ...}
[2025-10-17 14:55:43] 50 thèses récupérées depuis l’API.
[2025-10-17 14:55:44] 50 thèses insérées dans theses_staging (données brutes).
```

### Étape 2 : Vérifier le contenu SQL

```
SELECT titre, auteur, date_soutenance FROM theses_staging LIMIT 5;
```

Exemple :

| titre                                  | auteur       | date_soutenance |
| -------------------------------------- | ------------ | --------------- |
| Apprentissage profond et IA explicable | Dupont Marie | 2023-06-23      |
| IA générative et éthique               | Martin Jean  | 2022-10-12      |

---

## Partie 5 — Anonymisation

### Étape 1 : Lancer la fonction anonymisation

Le pipeline exécute automatiquement :

```python
anonymize_theses()
```

### Étape 2 : Vérifier la table anonymisée

```
SELECT titre, auteur_pseudo FROM theses_anonymisees LIMIT 3;
```

Exemple :

| titre                             | auteur_pseudo                            |
| --------------------------------- | ---------------------------------------- |
| Apprentissage profond et IA expl… | 7e240de74fb1ed08fa08d38063f6a6a91462a815 |
| IA générative et éthique          | 9c56cc51b1e6556a90f5b2195e1e2c15b7a12a8c |

Le champ `auteur_pseudo` est une empreinte SHA-256 non réversible.
Le titre est tronqué à 30 caractères pour éviter la ré-identification.

---

## Partie 6 — Rapport de conformité

### Étape 1 : Vérifier le fichier généré

Ouvrir :

```
logs/rapport_theses.txt
```

Exemple :

```
RAPPORT COMPLET — 2025-10-17 15:00:31
Thèses récupérées : 50
API : theses.fr (data.gouv.fr)
Étapes exécutées :
1. Audit du schéma → table audit_structure
2. Génération du dictionnaire → table dictionnaire_donnees
3. Insertion des données → table theses_staging
4. Anonymisation → table theses_anonymisees
Conformité : RGPD / ISO 27001 / ISO 20889 / AI Act
```

---

## Partie 7 — Évaluation finale
# Aucune eval pour cet excerice

## Partie 8 — Extension possible (travail bonus)

* Créer une vue de cartographie des relations :

  ```sql
  CREATE VIEW vw_carte_donnees AS
  SELECT 'theses_staging' AS table_source, 'auteur' AS colonne_source,
         'theses_anonymisees' AS table_cible, 'auteur_pseudo' AS colonne_cible;
  ```
* Exporter les tables `audit_structure` et `dictionnaire_donnees` en CSV :

  ```sql
  SELECT * FROM audit_structure
  INTO OUTFILE '/var/lib/mysql-files/audit_structure.csv'
  FIELDS TERMINATED BY ';' ENCLOSED BY '"' LINES TERMINATED BY '\n';
  ```

---

## Livrables attendus

1. Code complet Python : `main.py`
2. Base MySQL exportée : `theses_data.sql`
3. Log complet : `pipeline_theses.log`
4. Rapport final : `rapport_theses.txt`
5. (Optionnel) fichiers CSV `audit_structure.csv` et `dictionnaire_donnees.csv`

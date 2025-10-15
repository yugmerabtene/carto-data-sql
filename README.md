# Audit, cartographie et traitement des données avec SQL


**Pré-requis :** notions de base en SQL et compréhension des bases de données relationnelles
**Public visé :** analystes, data managers, auditeurs SI, développeurs, responsables conformité
**Outils utilisés :** MySQL 

---

## Objectifs généraux de la formation

* Réaliser un audit complet de bases de données (structure, cohérence, qualité, conformité).
* Concevoir une cartographie claire et exhaustive des données et de leurs relations.
* Traiter, filtrer et analyser les données par des requêtes SQL complexes.
* Identifier les non-conformités liées au RGPD et proposer des correctifs.
* Générer un rapport d’audit et de conformité structuré à des fins de documentation interne ou réglementaire.

---

## JOUR 1 — Module 1 : Audit et analyse des structures de données

### Objectifs pédagogiques

* Identifier les composants d’une base de données et comprendre leurs interactions.
* Détecter les incohérences, redondances et erreurs de conception.
* Mettre en place une première démarche d’audit de structure SQL.

### Contenu théorique

1. Principes de l’audit de données : qualité, performance, conformité.
2. Structure d’une base de données relationnelle : schéma, table, clé, contrainte, dépendance.
3. Analyse des relations entre entités : intégrité référentielle, clés étrangères, cardinalités.
4. Bonnes pratiques de conception et de normalisation des données (1NF, 2NF, 3NF, BCNF).
5. Méthodes et critères d’évaluation de la qualité structurelle d’une base.
6. Présentation des outils d’audit intégrés aux SGBD et méthodes d’automatisation des contrôles.

### Travaux pratiques (TP-01)

Audit structurel complet d’une base existante.
Les participants devront :

* Identifier les tables, colonnes, contraintes et relations.
* Repérer les champs non normalisés ou incohérents.
* Rédiger un tableau de synthèse présentant les anomalies détectées et leurs impacts.
* Documenter les résultats dans une table d’audit ou un fichier de rapport.

---

## JOUR 2 — Module 2 : Cartographie et gouvernance des données

### Objectifs pédagogiques

* Élaborer une cartographie logique et physique des données.
* Construire un dictionnaire des données à partir des métadonnées disponibles.
* Identifier les dépendances, les flux de données et les relations inter-tables.
* Mettre en œuvre une approche de gouvernance documentaire des données.

### Contenu théorique

1. Différences entre schémas conceptuel, logique et physique.
2. Méthodologie de cartographie des données au sein d’un système d’information.
3. Élaboration d’un dictionnaire des données : structure, sens, classification, usage.
4. Traçabilité et linéage : suivre les données de leur création à leur utilisation.
5. Documentation automatisée et extraction des métadonnées depuis les bases.
6. Introduction aux notions de sensibilité, de confidentialité et de classification (données personnelles, techniques, métiers).

### Travaux pratiques (TP-02)

Cartographie complète et création d’un dictionnaire de données.
Les participants devront :

* Lister toutes les tables et leurs colonnes.
* Déterminer la nature et la classification des données.
* Identifier les flux de données et les dépendances logiques.
* Produire un dictionnaire de données détaillé et une cartographie relationnelle complète.
* Ajouter des commentaires documentaires et des niveaux de sensibilité (public, interne, restreint).

---

## JOUR 3 — Module 3 : Traitement, qualité et audit de conformité des données

### Objectifs pédagogiques

* Identifier et corriger les anomalies de données (doublons, valeurs nulles, incohérences).
* Mettre en place un audit de la qualité et de la conformité des données.
* Réaliser des traitements analytiques complexes pour le reporting.
* Concevoir un rapport de conformité RGPD et qualité des données.

### Contenu théorique

1. Définition et typologie des anomalies : doublons, incohérences, valeurs manquantes, erreurs de format.
2. Méthodes d’analyse et de contrôle de la qualité des données.
3. Requêtes analytiques : agrégations, jointures, sous-requêtes, fonctions analytiques.
4. Mise en conformité RGPD : détection et pseudonymisation des données personnelles.
5. Mise en place d’un tableau de bord d’audit et d’indicateurs de conformité.
6. Élaboration d’un plan d’action correctif et de recommandations d’amélioration continue.

### Travaux pratiques (TP-03)

Audit de la qualité et de la conformité des données.
Les participants devront :

* Identifier les doublons et incohérences.
* Vérifier la conformité des données personnelles et techniques.
* Produire un rapport synthétique d’audit qualité et conformité.
* Mettre en œuvre des actions de correction et d’optimisation.
* Concevoir une procédure d’audit automatisé pour contrôle périodique.

---

## Projet fil rouge (à réaliser à la fin du jour 3)

**Sujet :**
Réaliser un audit complet et une cartographie automatisée de la base de données étudiée, incluant la classification des données, la détection des anomalies et la production d’un rapport final d’audit.

**Livrables attendus :**

* Rapport d’audit complet décrivant la structure, la qualité et la conformité des données.
* Dictionnaire de données à jour avec classification et commentaires.
* Cartographie des relations et flux de données.
* Recommandations techniques et organisationnelles.
* Proposition de plan d’amélioration continue.



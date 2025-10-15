# MODULE 02 — CARTOGRAPHIE ET GOUVERNANCE DES DONNÉES

## Durée

1 journée (7 heures)

## Objectifs pédagogiques

À l’issue de ce module, le participant sera capable de :

* Concevoir et maintenir une **cartographie complète des données**.
* Documenter la structure des tables et relations via un **dictionnaire de données automatisé**.
* Identifier les **flux et dépendances** entre les tables.
* Classer les données selon leur **sensibilité et usage (RGPD, sécurité, métier)**.
* Préparer une **documentation de gouvernance** pour le suivi, la conformité et les audits internes.

---

## 1. Rappel : Pourquoi cartographier ?

La **cartographie des données** est un élément central de la gouvernance d’un système d’information.
Elle permet de :

* Comprendre les liens logiques entre les entités (clients, factures, produits…).
* Identifier les points de collecte, stockage et diffusion de l’information.
* Visualiser la **traçabilité** et le **linéage des données**.
* Anticiper les impacts d’une modification dans une table sur le reste du système.
* Garantir la conformité RGPD en localisant les données personnelles et sensibles.

---

## 2. Dictionnaire de données : principes et structure

### 2.1. Définition

Le **dictionnaire de données** est un tableau détaillant les métadonnées descriptives d’une base.
Chaque ligne décrit :

* La table et la colonne concernée.
* Le type de données et les contraintes.
* L’usage métier ou technique.
* La sensibilité (publique, interne, restreinte, confidentielle).
* Les commentaires sur la qualité ou la conformité.

### 2.2. Exemple de structure

| Table   | Colonne | Type         | Nullable | Clé | Classification     | Commentaire                   |
| ------- | ------- | ------------ | -------- | --- | ------------------ | ----------------------------- |
| clients | email   | varchar(150) | OUI      | -   | Donnée personnelle | Doit être unique et non nulle |

---

## 3. Création du dictionnaire de données automatisé

### 3.1. Table `dictionnaire_donnees`

```sql
CREATE TABLE dictionnaire_donnees (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(100),
    column_name VARCHAR(100),
    data_type VARCHAR(50),
    is_nullable VARCHAR(3),
    constraint_type VARCHAR(50),
    classification VARCHAR(50),
    commentaire TEXT
);
```

### 3.2. Extraction automatique des métadonnées

Remplir automatiquement les colonnes de base depuis `information_schema` :

```sql
INSERT INTO dictionnaire_donnees (table_name, column_name, data_type, is_nullable)
SELECT table_name, column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public';
```

---

## 4. Classification et gouvernance des données

### 4.1. Catégories de classification

| Niveau         | Type de donnée          | Exemple                     | Mesure de protection              |
| -------------- | ----------------------- | --------------------------- | --------------------------------- |
| Publique       | Données non sensibles   | Catégories produits         | Aucune restriction                |
| Interne        | Données opérationnelles | ID, montants, dates         | Contrôle d’accès                  |
| Restreinte     | Données personnelles    | Noms, emails, téléphones    | Masquage, chiffrement             |
| Confidentielle | Données stratégiques    | Salaires, rapports internes | Chiffrement fort, accès restreint |

### 4.2. Ajout manuel de la classification

Une fois la table `dictionnaire_donnees` remplie, on complète la classification :

```sql
UPDATE dictionnaire_donnees
SET classification = CASE
    WHEN column_name IN ('nom', 'prenom', 'email', 'telephone') THEN 'Restreinte'
    WHEN column_name LIKE '%salaire%' THEN 'Confidentielle'
    WHEN column_name LIKE '%prix%' OR column_name LIKE '%montant%' THEN 'Interne'
    ELSE 'Publique'
END;
```

### 4.3. Ajout de commentaires métier

Les auditeurs peuvent compléter manuellement la colonne `commentaire` pour documenter le rôle et les contraintes :

```sql
UPDATE dictionnaire_donnees
SET commentaire = 'Donnée personnelle à pseudonymiser'
WHERE column_name = 'email';
```

---

## 5. Cartographie des relations et dépendances

### 5.1. Identifier les relations entre tables

Les relations peuvent être extraites de `information_schema.key_column_usage` ou reconstituées manuellement.
Par exemple, une relation logique existe entre :

* `clients.id_client` et `factures.id_client`
* `factures.id_facture` et `lignes_facture.id_facture`
* `lignes_facture.id_produit` et `produits.id_produit`

### 5.2. Création d’une vue de cartographie des liens

```sql
CREATE VIEW vw_carte_donnees AS
SELECT 
    'clients' AS table_source, 
    'id_client' AS colonne_source,
    'factures' AS table_cible,
    'id_client' AS colonne_cible
UNION
SELECT 'factures', 'id_facture', 'lignes_facture', 'id_facture'
UNION
SELECT 'produits', 'id_produit', 'lignes_facture', 'id_produit';
```

Cette vue permet de visualiser les liens existants ou manquants et servira plus tard à générer un **diagramme entité-relation (ERD)** dans un outil graphique (ex. DBeaver, MySQL Workbench).

---

## 6. Analyse de la couverture de documentation

L’objectif est de vérifier que toutes les colonnes du schéma ont bien été documentées.
Requête de contrôle :

```sql
SELECT table_name, COUNT(column_name) AS nb_colonnes, 
       COUNT(classification) AS nb_classes_remplies
FROM dictionnaire_donnees
GROUP BY table_name;
```

> Si `nb_classes_remplies < nb_colonnes`, la documentation est incomplète.

---

## 7. Travaux pratiques (TP-02) — Cartographie et dictionnaire automatisé

### Objectif général

Créer un **dictionnaire de données complet et automatisé**, puis en extraire une cartographie des relations.

### Étapes

1. Créer la table `dictionnaire_donnees`.
2. Extraire automatiquement les métadonnées des tables existantes.
3. Compléter la classification manuellement selon la nature des données.
4. Générer une vue des relations entre les tables principales.
5. Exporter les résultats vers un fichier CSV ou un tableur pour documentation.
6. Vérifier la couverture complète de la documentation (toutes les tables documentées).
7. Produire une synthèse expliquant :

   * Les relations entre entités ;
   * Les données personnelles ou sensibles ;
   * Les flux de données entre modules (ex. facturation → clients → produits).

### Résultats attendus

* Une table `dictionnaire_donnees` complète et correctement classifiée.
* Une vue `vw_carte_donnees` listant les relations principales.
* Un export CSV ou un rapport résumant la cartographie et les niveaux de sensibilité.
* Une proposition d’amélioration de la documentation (colonnes à renommer, types à corriger).

---

## 8. Interprétation et restitution

| Élément analysé                            | Observation                                   | Risque                    | Recommandation                      |
| ------------------------------------------ | --------------------------------------------- | ------------------------- | ----------------------------------- |
| `factures.id_client`                       | Pas de clé étrangère vers `clients.id_client` | Rupture d’intégrité       | Ajouter une contrainte FK           |
| `email` dans `clients`                     | Donnée personnelle non contrainte             | Non-conformité RGPD       | Ajouter UNIQUE / NOT NULL           |
| `prix` négatif                             | Valeur incohérente                            | Impact reporting          | Mettre en place contrôle applicatif |
| Données `date_inscription` au format texte | Mauvais typage                                | Risque d’erreur d’analyse | Conversion en `DATE`                |

---

## 9. Livrables attendus (fin du Module 02)

Chaque apprenant doit produire :

1. Le script SQL complet de création et d’alimentation du dictionnaire de données.
2. La vue de cartographie des relations (`vw_carte_donnees`).
3. Un rapport d’une à deux pages résumant la classification et les flux de données.
4. Une proposition de **plan de gouvernance** pour la documentation continue :

   * Procédures de mise à jour du dictionnaire après ajout de tables ou colonnes.
   * Révision périodique de la classification.
   * Sauvegarde du dictionnaire dans les livrables de conformité (RGPD, ISO 27001).

---

## 10. Conclusion du Module 02

À l’issue de ce module, les étudiants maîtrisent :

* La documentation automatisée des métadonnées.
* La construction d’un dictionnaire de données exploitable.
* L’identification des flux et dépendances entre entités.
* La classification et la gouvernance des données selon les exigences ISO et RGPD.

Ce module prépare directement la **troisième journée** :

> *Module 03 — Audit de qualité et conformité des données*,
> où les étudiants exploiteront cette cartographie pour **détecter, corriger et tracer** les anomalies de contenu.

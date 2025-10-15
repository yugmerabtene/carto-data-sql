# BASE DE DONNÉES PÉDAGOGIQUE : `entreprise_audit`

## 1. Contexte du projet

L’entreprise **DataConsult SARL** gère des **clients, produits, factures et employés**.
La base sert à **illustrer les audits de structure, de qualité et de conformité** dans un environnement SQL.

Certaines erreurs sont **intentionnellement présentes** pour que les étudiants puissent les détecter pendant les TPs :

* colonnes nullables injustifiées,
* doublons,
* données incohérentes,
* absence de contraintes,
* types inadaptés,
* clés étrangères manquantes.

---

## 2. Création de la base de données

```sql
CREATE DATABASE entreprise_audit;
USE entreprise_audit;
```

---

## 3. Structure des tables principales

### TABLE : clients

```sql
CREATE TABLE clients (
    id_client INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(100),
    prenom VARCHAR(100),
    email VARCHAR(150), -- manque contrainte UNIQUE et NOT NULL (à corriger)
    telephone VARCHAR(20),
    adresse TEXT,
    ville VARCHAR(100),
    code_postal VARCHAR(10),
    pays VARCHAR(50),
    date_inscription VARCHAR(20) -- devrait être de type DATE
);
```

### TABLE : produits

```sql
CREATE TABLE produits (
    id_produit INT AUTO_INCREMENT PRIMARY KEY,
    nom_produit VARCHAR(150),
    categorie VARCHAR(100),
    prix DECIMAL(10,2),
    stock INT,
    date_creation DATE
);
```

### TABLE : factures

```sql
CREATE TABLE factures (
    id_facture INT AUTO_INCREMENT PRIMARY KEY,
    id_client INT, -- clé étrangère manquante volontairement
    date_facture DATE,
    montant_total DECIMAL(10,2),
    statut VARCHAR(50),
    commentaire TEXT
);
```

### TABLE : lignes_facture

```sql
CREATE TABLE lignes_facture (
    id_ligne INT AUTO_INCREMENT PRIMARY KEY,
    id_facture INT,
    id_produit INT,
    quantite INT,
    prix_unitaire DECIMAL(10,2)
);
```

### TABLE : employes

```sql
CREATE TABLE employes (
    id_employe INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(100),
    prenom VARCHAR(100),
    email VARCHAR(150),
    poste VARCHAR(100),
    salaire DECIMAL(10,2),
    departement VARCHAR(100),
    date_embauche DATE,
    manager_id INT
);
```

### TABLE : departements

```sql
CREATE TABLE departements (
    id_departement INT AUTO_INCREMENT PRIMARY KEY,
    nom_departement VARCHAR(100),
    localisation VARCHAR(100)
);
```

---

## 4. Insertion de données initiales

### Clients

```sql
INSERT INTO clients (nom, prenom, email, telephone, adresse, ville, code_postal, pays, date_inscription)
VALUES
('Durand', 'Paul', 'paul.durand@gmail.com', '0612345678', '10 rue de Paris', 'Paris', '75001', 'France', '2022-01-05'),
('Dupont', 'Marie', 'marie.dupont@gmail.com', '0622334455', '5 avenue Victor Hugo', 'Lyon', '69003', 'France', '2021-11-20'),
('Dupont', 'Marie', 'marie.dupont@gmail.com', '0622334455', '5 avenue Victor Hugo', 'Lyon', '69003', 'France', '2021-11-20'), -- doublon volontaire
('Smith', 'John', NULL, '0700000000', 'Unknown', 'Londres', 'N/A', 'UK', '01/03/2021'); -- email NULL et format de date incohérent
```

### Produits

```sql
INSERT INTO produits (nom_produit, categorie, prix, stock, date_creation)
VALUES
('Ordinateur portable', 'Informatique', 850.00, 12, '2022-04-10'),
('Clavier mécanique', 'Informatique', 120.00, 45, '2023-01-02'),
('Ecran 27 pouces', 'Informatique', -320.00, 20, '2021-12-30'), -- prix négatif volontaire
('Bureau bois', 'Mobilier', 250.00, 5, '2023-03-15');
```

### Factures

```sql
INSERT INTO factures (id_client, date_facture, montant_total, statut, commentaire)
VALUES
(1, '2022-06-12', 970.00, 'Payée', NULL),
(2, '2022-08-22', 250.00, 'En attente', 'Client souhaite un duplicata'),
(5, '2022-09-15', 300.00, 'Payée', 'Client inexistant'); -- client 5 n’existe pas
```

### Lignes facture

```sql
INSERT INTO lignes_facture (id_facture, id_produit, quantite, prix_unitaire)
VALUES
(1, 1, 1, 850.00),
(1, 2, 1, 120.00),
(2, 4, 1, 250.00),
(3, 3, 1, 300.00);
```

### Employés

```sql
INSERT INTO employes (nom, prenom, email, poste, salaire, departement, date_embauche, manager_id)
VALUES
('Martin', 'Sophie', 'sophie.martin@data.fr', 'Comptable', 2800.00, 'Finance', '2020-03-15', NULL),
('Leclerc', 'Pierre', 'pierre.leclerc@data.fr', 'Commercial', 3200.00, 'Ventes', '2021-05-01', 1),
('Bernard', 'Luc', 'luc.bernard@data.fr', 'IT Support', 2900.00, 'Informatique', '2019-07-12', 1),
('Lambert', 'Anne', NULL, 'RH', 3100.00, 'Ressources Humaines', '2022-01-10', NULL); -- email manquant
```

### Départements

```sql
INSERT INTO departements (nom_departement, localisation)
VALUES
('Finance', 'Paris'),
('Ventes', 'Lyon'),
('Informatique', 'Marseille'),
('Ressources Humaines', 'Paris');
```

---

## 5. Objectifs pédagogiques pour le Module 01 (avec ces données)

Les étudiants devront :

1. **Lister toutes les tables et colonnes** du schéma.

   * Découvrir les incohérences de structure (ex. `date_inscription` en VARCHAR).
2. **Vérifier les contraintes primaires et étrangères.**

   * Constat : les tables `factures` et `lignes_facture` ne possèdent pas de clés étrangères.
3. **Analyser la qualité des colonnes critiques.**

   * `email` nullable, absence de `UNIQUE`, formats incohérents.
4. **Détecter les doublons dans `clients`.**

   * Présence de deux enregistrements identiques.
5. **Repérer les valeurs incohérentes.**

   * Prix négatif dans `produits`, client inexistant dans `factures`.
6. **Créer une table d’audit et y enregistrer les anomalies détectées.**
7. **Produire un rapport final** listant :

   * Nom de la table
   * Colonne concernée
   * Type d’anomalie
   * Commentaire de correction

---

## 6. Exemples de requêtes typiques pour l’audit structurel

### Identifier les tables sans clé étrangère

```sql
SELECT table_name
FROM information_schema.tables
WHERE table_name NOT IN (
    SELECT DISTINCT table_name 
    FROM information_schema.table_constraints 
    WHERE constraint_type = 'FOREIGN KEY'
);
```

### Lister les colonnes pouvant poser problème

```sql
SELECT table_name, column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public'
AND (is_nullable = 'YES' OR data_type LIKE '%char%');
```

### Trouver les doublons dans les clients

```sql
SELECT nom, prenom, email, COUNT(*) AS nb
FROM clients
GROUP BY nom, prenom, email
HAVING COUNT(*) > 1;
```

### Vérifier les relations orphelines

```sql
SELECT f.id_facture, f.id_client
FROM factures f
LEFT JOIN clients c ON f.id_client = c.id_client
WHERE c.id_client IS NULL;
```

### Créer et remplir le rapport d’audit

```sql
CREATE TABLE audit_structure (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(100),
    colonne VARCHAR(100),
    anomalie VARCHAR(200),
    commentaire TEXT,
    date_audit TIMESTAMP DEFAULT NOW()
);

INSERT INTO audit_structure (table_name, colonne, anomalie, commentaire)
VALUES
('clients', 'email', 'Nullable', 'Doit être NOT NULL et UNIQUE'),
('produits', 'prix', 'Valeur négative', 'Prix inférieur à zéro à corriger'),
('factures', 'id_client', 'Clé étrangère manquante', 'Doit référencer clients.id_client');
```

---

## 7. Livrable attendu (fin du Module 01)

À la fin de ce module, chaque apprenant doit remettre :

1. Le **script SQL complet** d’audit (`audit_module_01.sql`), incluant :

   * Création de la table `audit_structure`
   * Les requêtes de détection
   * Les insertions d’anomalies
2. Un **rapport d’audit** (CSV ou tableau) listant toutes les incohérences détectées.
3. Des **recommandations de correction** :

   * Ajout de contraintes (NOT NULL, UNIQUE, FOREIGN KEY)
   * Normalisation des types (`VARCHAR` → `DATE`)
   * Nettoyage des doublons
   * Validation des formats d’email

---

## 8. Objectifs

À la fin du **Module 01**, les étudiants auront compris :

* Comment auditer la structure d’une base SQL.
* Comment automatiser la détection des anomalies.
* Comment préparer les bases pour la **cartographie et la gouvernance des données (Module 02)**.



# MODULE 03 — AUDIT DE LA QUALITÉ ET CONFORMITÉ DES DONNÉES

## Durée

1 journée (7 heures)

## Objectifs pédagogiques

À l’issue de ce module, le participant sera capable de :

* Détecter les **anomalies de contenu** (doublons, incohérences, valeurs manquantes, formats incorrects).
* Mettre en œuvre des **traitements SQL avancés** pour corriger ou tracer ces anomalies.
* Effectuer un **audit de conformité RGPD et ISO 27001** sur les données sensibles.
* Concevoir un **rapport de qualité et conformité** intégrant indicateurs, risques et recommandations.
* Automatiser le contrôle périodique via une procédure stockée ou un script SQL.

---

## 1. Introduction : de la structure à la qualité

Les modules précédents ont porté sur la **structure** et la **cartographie** ; ce module se concentre sur le **contenu des données**.
Un système peut être bien modélisé, mais **non fiable** si les données sont incomplètes ou erronées.

### Types d’anomalies fréquentes

| Type                      | Exemple                                  | Impact                                    |
| ------------------------- | ---------------------------------------- | ----------------------------------------- |
| Doublons                  | Clients enregistrés deux fois            | Fausse volumétrie, erreurs de facturation |
| Valeurs NULL injustifiées | Email client absent                      | Rupture de contact / RGPD                 |
| Incohérences logiques     | Date d’embauche future, prix négatif     | Erreur métier                             |
| Mauvais format            | Numéro de téléphone ou date mal formatée | Non-conformité RGPD                       |
| Données orphelines        | Factures sans client associé             | Perte de traçabilité                      |

---

## 2. Vérification de la complétude et des doublons

### 2.1. Recherche de valeurs nulles critiques

```sql
SELECT table_name, column_name, COUNT(*) AS nb_nulls
FROM information_schema.columns c
JOIN (
    SELECT 'clients' AS table_name, 'email' AS column_name UNION
    SELECT 'clients', 'nom' UNION
    SELECT 'factures', 'id_client'
) AS crit ON c.table_name = crit.table_name AND c.column_name = crit.column_name
JOIN clients cl ON 1=1
WHERE cl.email IS NULL;
```

> Cette requête peut être adaptée pour tout champ critique identifié dans le dictionnaire de données.

### 2.2. Détection des doublons

```sql
SELECT nom, prenom, email, COUNT(*) AS nb
FROM clients
GROUP BY nom, prenom, email
HAVING COUNT(*) > 1;
```

### 2.3. Suppression contrôlée des doublons

Avant toute suppression, sauvegarder les lignes concernées :

```sql
CREATE TABLE clients_doublons AS
SELECT * FROM clients
WHERE email IN (
    SELECT email FROM clients GROUP BY email HAVING COUNT(*) > 1
);
```

Puis effectuer un nettoyage manuel ou via un identifiant prioritaire (ex. la plus récente).

---

## 3. Contrôle d’intégrité référentielle

### 3.1. Vérification des relations manquantes

```sql
SELECT f.id_facture, f.id_client
FROM factures f
LEFT JOIN clients c ON f.id_client = c.id_client
WHERE c.id_client IS NULL;
```

### 3.2. Vérification de cohérence entre lignes et factures

```sql
SELECT l.id_ligne, l.id_facture
FROM lignes_facture l
LEFT JOIN factures f ON l.id_facture = f.id_facture
WHERE f.id_facture IS NULL;
```

### 3.3. Validation des montants totaux

Comparer le montant total enregistré dans la facture avec la somme des lignes :

```sql
SELECT f.id_facture, f.montant_total, 
       SUM(l.quantite * l.prix_unitaire) AS total_calculé
FROM factures f
JOIN lignes_facture l ON f.id_facture = l.id_facture
GROUP BY f.id_facture, f.montant_total
HAVING f.montant_total <> SUM(l.quantite * l.prix_unitaire);
```

---

## 4. Vérification des formats et des types de données

### 4.1. Format d’adresse e-mail

```sql
SELECT id_client, email
FROM clients
WHERE email NOT REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'
   OR email IS NULL;
```

### 4.2. Format de date

```sql
SELECT id_client, date_inscription
FROM clients
WHERE date_inscription NOT REGEXP '^[0-9]{4}-[0-9]{2}-[0-9]{2}$';
```

> La présence de formats incohérents (`'01/03/2021'`) doit être signalée dans le rapport.

### 4.3. Données numériques incohérentes

```sql
SELECT id_produit, nom_produit, prix
FROM produits
WHERE prix <= 0;
```

---

## 5. Audit RGPD et conformité ISO 27001

### 5.1. Identification des données personnelles

Se baser sur la classification du dictionnaire (`Restreinte` ou `Confidentielle`) :

```sql
SELECT table_name, column_name
FROM dictionnaire_donnees
WHERE classification IN ('Restreinte', 'Confidentielle');
```

### 5.2. Vérification de la pseudonymisation / anonymisation

Par exemple, les colonnes `email` ou `telephone` doivent être chiffrées, hachées ou pseudonymisées dans les exports.
On vérifie si des champs sensibles apparaissent dans des vues publiques :

```sql
SELECT table_name, column_name
FROM information_schema.columns
WHERE table_name LIKE 'vw_%'
AND column_name IN ('email', 'telephone', 'adresse');
```

### 5.3. Vérification de la suppression logique

Conformément à l’article 17 du RGPD, certaines données doivent être supprimées après inactivité :

```sql
SELECT id_client, nom, prenom, date_inscription
FROM clients
WHERE date_inscription < DATE_SUB(NOW(), INTERVAL 5 YEAR);
```

> Les enregistrements doivent être archivés ou supprimés selon la politique de rétention.

---

## 6. Création du rapport d’audit qualité et conformité

### 6.1. Table `rapport_audit`

```sql
CREATE TABLE rapport_audit (
    id SERIAL PRIMARY KEY,
    type_anomalie VARCHAR(100),
    table_name VARCHAR(100),
    colonne VARCHAR(100),
    nb_cas INT,
    commentaire TEXT,
    date_audit TIMESTAMP DEFAULT NOW()
);
```

### 6.2. Enregistrement des résultats

Exemples :

```sql
INSERT INTO rapport_audit (type_anomalie, table_name, colonne, nb_cas, commentaire)
VALUES
('Doublons détectés', 'clients', 'email', 2, 'Même adresse utilisée plusieurs fois'),
('Valeurs NULL', 'clients', 'email', 1, 'Email manquant sur un client'),
('Prix incohérent', 'produits', 'prix', 1, 'Valeur négative');
```

### 6.3. Rapport consolidé

```sql
SELECT type_anomalie, table_name, COUNT(*) AS nb_lignes, MAX(date_audit) AS dernier_audit
FROM rapport_audit
GROUP BY type_anomalie, table_name;
```

---

## 7. Automatisation de l’audit global

Pour automatiser l’audit, créer une procédure stockée regroupant tous les contrôles :

```sql
DELIMITER //
CREATE PROCEDURE sp_audit_global()
BEGIN
    DELETE FROM rapport_audit;

    INSERT INTO rapport_audit (type_anomalie, table_name, colonne, nb_cas, commentaire)
    SELECT 'Doublons email', 'clients', 'email', COUNT(*), 'Adresses email non uniques'
    FROM clients
    GROUP BY email
    HAVING COUNT(*) > 1;

    INSERT INTO rapport_audit (type_anomalie, table_name, colonne, nb_cas, commentaire)
    SELECT 'Valeurs NULL', 'clients', 'email', COUNT(*), 'Emails manquants'
    FROM clients WHERE email IS NULL;

    INSERT INTO rapport_audit (type_anomalie, table_name, colonne, nb_cas, commentaire)
    SELECT 'Prix incohérent', 'produits', 'prix', COUNT(*), 'Prix <= 0'
    FROM produits WHERE prix <= 0;
END //
DELIMITER ;
```

Exécution :

```sql
CALL sp_audit_global();
SELECT * FROM rapport_audit;
```

---

## 8. LAB-03 — Audit qualité et conformité SQL

### Objectif général

Mettre en œuvre un **audit complet du contenu** de la base `entreprise_audit`, incluant vérification, correction et reporting.

### Étapes

1. Exécuter les requêtes de vérification (doublons, NULL, incohérences).
2. Compléter la table `rapport_audit` avec les anomalies détectées.
3. Mettre en place la procédure `sp_audit_global()`.
4. Exécuter la procédure et produire le rapport synthétique.
5. Identifier les non-conformités RGPD et proposer des mesures correctives.
6. Exporter le rapport final (CSV, tableur ou PDF).

### Résultats attendus

* Une table `rapport_audit` documentée et complète.
* Un fichier d’export listant toutes les anomalies et leurs fréquences.
* Des propositions d’amélioration :

  * ajout de contraintes,
  * correction de types,
  * suppression ou anonymisation des données sensibles,
  * amélioration des formats.

---

## 9. Exemple de rapport final (synthèse)

| Type d’anomalie   | Table    | Colonne          | Nb de cas | Risque              | Recommandation           |
| ----------------- | -------- | ---------------- | --------- | ------------------- | ------------------------ |
| Doublons email    | clients  | email            | 2         | RGPD / cohérence    | Contrôle UNIQUE          |
| Valeurs NULL      | clients  | email            | 1         | Contact impossible  | NOT NULL                 |
| Prix incohérent   | produits | prix             | 1         | Impact sur factures | Vérification applicative |
| Client inexistant | factures | id_client        | 1         | Rupture d’intégrité | Ajout de clé étrangère   |
| Date texte        | clients  | date_inscription | 1         | Erreur analytique   | Conversion en DATE       |

---

## 10. Livrables attendus (fin de module)

Chaque participant doit remettre :

1. Le script SQL complet d’audit (`audit_module_03.sql`).
2. Le rapport global (`rapport_audit.csv` ou `.xlsx`).
3. Une synthèse écrite de 2 pages avec :

   * Résultats clés de l’audit ;
   * Analyse des risques ;
   * Recommandations RGPD et ISO 27001.
4. Un plan d’amélioration continue :

   * Automatisation du contrôle périodique ;
   * Suivi mensuel des anomalies ;
   * Archivage des rapports.

---

## 11. Évaluation des compétences

| Domaine             | Compétence évaluée                                | Niveau attendu |
| ------------------- | ------------------------------------------------- | -------------- |
| Audit de contenu    | Identifier et documenter les anomalies de données | Avancé         |
| Qualité des données | Mettre en œuvre des traitements SQL correctifs    | Avancé         |
| Conformité RGPD     | Détecter les données personnelles non protégées   | Intermédiaire  |
| Reporting           | Générer un rapport synthétique et exploitable     | Avancé         |
| Gouvernance         | Intégrer les résultats à la politique qualité     | Avancé         |

---

## 12. Conclusion du Module 03

À la fin de ce module, le participant maîtrise :

* Les techniques d’audit complet (structure + contenu).
* L’automatisation et le reporting de la qualité des données.
* La mise en conformité RGPD et ISO 27001 dans les bases SQL.
* L’intégration de l’audit dans la gouvernance globale de l’entreprise.

Ce module clôture le parcours.
Les trois journées peuvent se conclure par un **projet fil rouge** :

> “Réaliser un audit complet (structure, cartographie, qualité) d’une base de données d’entreprise, produire les rapports associés et formuler un plan d’amélioration de la gouvernance des données.”

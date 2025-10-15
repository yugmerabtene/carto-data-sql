## 1. Références normatives à respecter

### RGPD

* **Article 4 (1) et (5)** : définition de l’anonymisation et pseudonymisation.
* **Article 25** : *Privacy by Design & by Default*.
* **Article 32** : sécurité des traitements (chiffrement, intégrité, confidentialité).
* **Article 35** : AIPD obligatoire pour tout traitement à haut risque (ex : IA).
* **Guidelines CNIL (2019)** : « Comment anonymiser des données ? ».

### ISO/IEC applicables

| Référence              | Objet                                          | Application                                     |
| ---------------------- | ---------------------------------------------- | ----------------------------------------------- |
| **ISO/IEC 27001:2022** | Système de management de la sécurité           | Cadre global (Annexe A.5, A.8)                  |
| **ISO/IEC 27701:2019** | Extension vie privée du 27001                  | RGPD + gouvernance de la donnée                 |
| **ISO/IEC 20889:2018** | Techniques d’anonymisation et pseudonymisation | Méthodologies de masquage                       |
| **ISO/IEC 29100:2011** | Privacy framework                              | Définitions et principes                        |
| **ISO/IEC 23894:2023** | Gestion du risque IA                           | Alignement AI Act                               |
| **AI Act (UE 2024)**   | Réglementation IA par niveau de risque         | Obligation de traçabilité et de confidentialité |

---

## 2. Outils d’anonymisation compatibles MySQL

### 🔹 A. Outils open source

| Outil                                    | Description                                                                 | Conformité          | Points forts                                        |
| ---------------------------------------- | --------------------------------------------------------------------------- | ------------------- | --------------------------------------------------- |
| **Aircloak Insights**                    | Anonymisation différentielle via proxy SQL compatible MySQL                 | RGPD, ISO/IEC 20889 | Respect des statistiques, irréversibilité           |
| **ARX Data Anonymization Tool**          | Outil Java complet pour anonymisation k-anonymity, l-diversity, t-closeness | ISO/IEC 20889, CNIL | Interface graphique, compatible CSV/SQL             |
| **Anonymizer (MySQL Anonymizer)**        | Script Python open source pour MySQL avec masquage de colonnes              | RGPD, ISO/IEC 27001 | Facile à intégrer dans pipeline Dev                 |
| **Anonimatron**                          | Générateur de données fictives pour bases SQL                               | RGPD                | Automatisation dans CI/CD                           |
| **Faker + SQLAlchemy**                   | Générateur de données pseudonymisées (Python)                               | RGPD                | Facile à coupler à ETL                              |
| **DataAnon**                             | Framework Ruby/Java pour masquage de bases MySQL                            | RGPD                | Support natif MySQL et PostgreSQL                   |
| **Synthia / SDV (Synthetic Data Vault)** | Génération de données synthétiques IA                                       | ISO 20889, AI Act   | Remplace les données réelles sans fuite identitaire |

---

### 🔹 B. Outils professionnels (SaaS ou On-premise)

| Outil                                            | Fonctionnalités principales                     | Conformité               | Particularité                    |
| ------------------------------------------------ | ----------------------------------------------- | ------------------------ | -------------------------------- |
| **Dataguise (DQ Secure)**                        | Découverte, masquage, chiffrement, audit        | ISO 27001 / RGPD / HIPAA | Multi-SGBD (MySQL, Oracle, etc.) |
| **Informatica Data Privacy Management**          | Masquage dynamique, anonymisation, Data Catalog | ISO 27701                | Intégré à Data Governance        |
| **IBM Data Privacy Passports**                   | Pseudonymisation cryptographique                | ISO 20889, RGPD          | Gestion fine des accès           |
| **Delphix Data Masking**                         | Masquage irréversible des jeux de test MySQL    | ISO 27001                | Utilisé en DevSecOps             |
| **Oracle Data Safe** (compatible MySQL HeatWave) | Masquage et évaluation du risque                | RGPD / ISO 27018         | Outil cloud certifié             |
| **Tonic.ai / Mostly AI**                         | Génération de données synthétiques IA           | AI Act / ISO 23894       | Préparation dataset IA anonymes  |

---

## 3. Techniques SQL et procédurales pour MySQL

Ces méthodes peuvent être utilisées en interne (par script ou procédure stockée) dans un cadre ISO/27001.

### A. Pseudonymisation par hachage

Irreversible hashing des identifiants (ex : email, ID utilisateur)

```sql
UPDATE clients
SET email = SHA2(email, 256);
```

> Conforme à l’article 32 RGPD si la clé n’est pas réversible.

---

### B. Pseudonymisation réversible (clé stockée à part)

```sql
CREATE TABLE pseudonymes (
    id_pseudo INT AUTO_INCREMENT PRIMARY KEY,
    id_original INT,
    token VARCHAR(64),
    date_creation TIMESTAMP DEFAULT NOW()
);
UPDATE clients
SET email = CONCAT('user_', id_client, '@example.com');
```

> La correspondance est conservée dans une table séparée et chiffrée, accessible uniquement au DPO.

---

### C. Masquage partiel des données

```sql
UPDATE clients
SET email = CONCAT(SUBSTRING(email,1,3), '***@', SUBSTRING_INDEX(email,'@',-1));
```

> Méthode utilisée pour rendre les données visibles à des fins de tests tout en limitant l’exposition.

---

### D. Substitution aléatoire / Génération fictive

```sql
UPDATE clients
SET nom = ELT(FLOOR(1 + RAND() * 5), 'Durand','Martin','Bernard','Dupont','Moreau'),
    prenom = ELT(FLOOR(1 + RAND() * 5), 'Paul','Marie','Luc','Claire','Olivier');
```

> Remplace les identités réelles par des valeurs générées aléatoirement.

---

### E. Données synthétiques (recommandé pour IA)

1. Extraire le schéma des tables.
2. Générer des données simulées via **SDV (Synthetic Data Vault)** ou **Faker**.
3. Charger ces jeux anonymisés dans un environnement d’apprentissage.

> Cette méthode permet d’entraîner des modèles IA sans jamais manipuler de données personnelles.

---

## 4. Bonnes pratiques ISO / RGPD / AI Act

### A. Avant l’anonymisation

* Identifier les données personnelles via une **cartographie et un dictionnaire**.
* Classer les colonnes selon la sensibilité (publique, interne, restreinte, confidentielle).
* Réaliser une **AIPD** (analyse d’impact) si les données concernent des individus.

### B. Pendant l’anonymisation

* Documenter la méthode (type de transformation, algorithme, date, responsable).
* Conserver un **registre d’anonymisation** (Annexe A.5.33 ISO/IEC 27001).
* Vérifier que les données transformées restent **utilisables pour l’objectif IA**.

### C. Après l’anonymisation

* Tester la **non-réidentifiabilité** par échantillonnage.
* Supprimer les clés de correspondance (si anonymisation totale).
* Archiver les logs de traitement dans le **registre du SMSI (ISO 27001 A.5.36)**.

### D. Pour les projets IA

* Les datasets d’entraînement doivent être **traçables** (AI Act, art. 9).
* Les biais issus des données doivent être **documentés et corrigés**.
* Les jeux IA doivent être **anonymisés avant export hors UE**.
* Tenir un **registre des modèles et versions de jeux** (AI Model Card / Data Sheet).

---

## 5. Architecture type conforme ISO + RGPD + AI Act

```
        +-----------------------------+
        |   BASE DE DONNÉES MySQL     |
        +-----------------------------+
                  |
                  v
        +-----------------------------+
        |   Extraction ETL sécurisée  |
        |   (Anonymizer, ARX, Faker)  |
        +-----------------------------+
                  |
                  v
        +-----------------------------+
        |  Zone de staging / test     |
        |  Données pseudonymisées     |
        +-----------------------------+
                  |
                  v
        +-----------------------------+
        |  Zone IA / Data Lake        |
        |  Données anonymisées ou     |
        |  synthétiques (SDV, Tonic)  |
        +-----------------------------+
                  |
                  v
        +-----------------------------+
        |  Audit & Registre conformité|
        |  (RGPD / ISO / AI Act)      |
        +-----------------------------+
```

> Chaque zone dispose de contrôles RBAC, chiffrement AES-256, et journalisation des accès (ISO/IEC 27001 A.8.15, A.8.16).

---

## 6. Recommandations pratiques pour MySQL en production

1. **Éviter les exports CSV** bruts de données sensibles.
2. Utiliser un **proxy d’anonymisation (Aircloak)** ou un **pipeline ETL** dédié.
3. Limiter les droits d’accès aux scripts et procédures anonymisantes.
4. Automatiser les contrôles via un **CRON + rapport d’audit SQL**.
5. Tester régulièrement la **résistance à la réidentification**.
6. Documenter chaque action dans un **registre du SMSI**.
7. Pour l’IA, préférer les **jeux synthétiques validés** (pas simplement pseudonymisés).

---

## 7. Exemple de pipeline d’anonymisation certifiable ISO 27001

1. **Phase 1 — Identification :**
   Extraire les colonnes sensibles via le dictionnaire de données.

2. **Phase 2 — Pré-traitement :**
   Appliquer un script MySQL ou ARX pour pseudonymisation/anonymisation.

3. **Phase 3 — Contrôle :**
   Comparer le résultat avec les critères de qualité et d’irréversibilité.

4. **Phase 4 — Validation :**
   Le DPO ou RSSI valide la conformité selon la politique interne.

5. **Phase 5 — Documentation :**
   Archivage du rapport dans le SMSI et dans le registre AIPD.

---

## 8. Outils recommandés pour une conformité complète MySQL + IA

| Usage                    | Outil                                         | Type                      | Conforme           |
| ------------------------ | --------------------------------------------- | ------------------------- | ------------------ |
| Audit & cartographie     | Collibra / Talend Data Catalog                | Data Governance           | ISO 27001 / RGPD   |
| Anonymisation / masquage | ARX / Anonimatron / Delphix                   | Masquage / Synthèse       | ISO 20889          |
| Génération IA            | MostlyAI / Tonic.ai / SDV                     | Données synthétiques      | AI Act / ISO 23894 |
| Contrôle RGPD            | CNIL PIA / OneTrust                           | AIPD & Registre           | RGPD / ISO 27701   |
| Sécurité MySQL           | Hashicorp Vault + MySQL Enterprise Encryption | Chiffrement / Secret mgmt | ISO 27001          |
| Documentation            | Confluence / Nextcloud + RACI                 | Gouvernance documentaire  | ISO 27001 / 27701  |

---

## 9. En résumé — Politique d’anonymisation MySQL conforme RGPD / ISO / AI

**Objectif :**

> Garantir la confidentialité, l’intégrité, la traçabilité et la conformité des données utilisées dans les environnements IA.

**Piliers :**

1. **Cartographier** toutes les données sensibles (RGPD Art. 30).
2. **Anonymiser ou pseudonymiser** selon la finalité et le besoin de réversibilité.
3. **Documenter** toutes les transformations (registre SMSI, AIPD).
4. **Automatiser et auditer** régulièrement les scripts d’anonymisation.
5. **Former** les équipes data et IA à la culture de la donnée responsable.


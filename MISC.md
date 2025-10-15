# MODULE — CARTOGRAPHIE, GOUVERNANCE ET SÉCURITÉ DES DONNÉES POUR L’INTELLIGENCE ARTIFICIELLE

**Public visé :** architectes data, auditeurs SI, data scientists, RSSI, chefs de projet IA
**Référentiels :** ISO/IEC 27001 :2022, ISO/IEC 27701, ISO/IEC 29100, ISO/IEC 23894, RGPD, AI Act

---

## Objectifs pédagogiques

À l’issue du module, l’apprenant sera capable de :

1. Expliquer le rôle stratégique de la **cartographie des données** dans un projet IA.
2. Identifier les **flux, dépendances, traitements et zones de risque** liés aux jeux de données.
3. Mettre en place une **anonymisation et pseudonymisation conformes** au RGPD et à l’ISO.
4. Concevoir une **gouvernance des données IA** compatible avec les obligations du **AI Act**.
5. Garantir la **sécurité, la traçabilité et la conformité** des traitements de données d’apprentissage.

---

## 1. Introduction : la donnée, matière première de l’IA

L’intelligence artificielle repose sur des volumes massifs de données, structurées ou non.
Mais toute IA est **aussi éthique et fiable que la qualité et la conformité** des données qu’elle exploite.

La cartographie est l’étape fondatrice de la **Data Governance**, car elle répond à 3 questions clés :

* **Quelles données sont utilisées ?** (origine, type, nature, sensibilité)
* **Où circulent-elles ?** (flux, traitements, stockage, partage)
* **Qui y accède et pourquoi ?** (responsabilités, rôles, durées, finalités)

Sans cette vision, impossible d’assurer :

* la **traçabilité** des jeux d’apprentissage ;
* le **contrôle des droits et durées** ;
* la **conformité** vis-à-vis du RGPD et de l’AI Act.

---

## 2. Cartographier les données : principes et méthode

### 2.1. Objectifs de la cartographie

* Identifier toutes les sources, tables, fichiers et flux manipulés.
* Documenter la nature, la sensibilité et la finalité des données.
* Visualiser les dépendances entre systèmes (collecte, traitement, archivage).
* Créer une base de référence pour les audits et l’amélioration continue.

### 2.2. Étapes de la cartographie

1. **Recensement** : inventaire des données, bases, fichiers et API.
2. **Classification** : typologie (personnelle, technique, métier, publique).
3. **Traçabilité** : description des flux, échanges et transformations.
4. **Documentation** : dictionnaire de données et fiches de traitement.
5. **Visualisation** : schémas, matrices de flux, graphes relationnels.

### 2.3. Livrables typiques

* Dictionnaire des données (source, type, sensibilité, finalité, responsable).
* Schéma des flux de données entre modules applicatifs et partenaires.
* Tableau de correspondance entre traitements IA et bases sous-jacentes.

---

## 3. Importance de la cartographie dans les projets d’IA

### 3.1. Transparence et traçabilité

L’**AI Act (articles 9 à 15)** impose la **traçabilité des données d’entraînement et d’évaluation**.
Une IA doit pouvoir expliquer :

* l’origine et la représentativité des données ;
* les transformations subies ;
* la conformité des jeux aux droits des personnes.

### 3.2. Gestion du risque et auditabilité

La cartographie sert de socle à :

* la **matrice de risques IA** (biais, sécurité, atteinte aux droits) ;
* les **audits de performance et de conformité** ;
* les **revues éthiques** et la gestion du cycle de vie des modèles.

### 3.3. Qualité et fiabilité

Une IA mal alimentée (données redondantes, biaisées, incomplètes) produit des décisions erronées.
La cartographie permet de :

* contrôler la **qualité et la cohérence** ;
* mesurer la **provenance** ;
* garantir la **réutilisabilité et la mise à jour** des jeux.

---

## 4. Anonymisation et pseudonymisation des données

### 4.1. Définitions RGPD

| Concept              | Description                                                              | Réversibilité                                      |
| -------------------- | ------------------------------------------------------------------------ | -------------------------------------------------- |
| **Anonymisation**    | Processus rendant impossible toute réidentification directe ou indirecte | Irréversible                                       |
| **Pseudonymisation** | Remplacement des identifiants directs par des pseudonymes ou clés        | Réversible (via table de correspondance sécurisée) |

### 4.2. Techniques principales

* Suppression ou hachage des identifiants (SHA-256, SHA-512).
* Masquage partiel des champs (`paul.durand@…`).
* Tokenisation ou clé de correspondance (pseudonyme aléatoire).
* Agrégation statistique (moyennes, plages d’âges).
* Perturbation différentielle (ajout de bruit contrôlé).

### 4.3. Exigences de conformité

* L’anonymisation doit **empêcher toute ré-identification** raisonnablement possible.
* Les clés de pseudonymisation doivent être **stockées séparément** et **chiffrées**.
* Les traitements IA utilisant ces données doivent **préserver la finalité initiale**.
* Les jeux doivent être **documentés** (méthode, outil, version, date, responsable).

### 4.4. Référentiels normatifs

* **ISO/IEC 20889 :2018** — Techniques de pseudonymisation et d’anonymisation.
* **ISO/IEC 29100 :2011** — Privacy Framework.
* **CNIL – Méthodologie d’anonymisation (2019)**.

---

## 5. Bonnes pratiques de sécurité et conformité

### 5.1. Gouvernance selon l’ISO/IEC 27001 :2022

Les annexes A.5 et A.8 imposent de :

* Tenir un **registre des actifs et des données** (A.5.9).
* Classer les informations par **niveau de sensibilité** (A.5.12).
* Assurer le **chiffrement des données en transit et au repos** (A.8.24).
* Documenter les **relations contractuelles et transferts de données** (A.5.31).

### 5.2. Alignement RGPD

* **Base légale** pour tout traitement (article 6).
* **Principe de minimisation** (article 5) : seules les données nécessaires.
* **Droits des personnes** (articles 12–22) : accès, rectification, effacement.
* **Analyse d’impact (AIPD)** obligatoire pour tout traitement à haut risque (article 35).

### 5.3. Alignement AI Act

* Les systèmes d’IA « à haut risque » doivent :

  * tracer les données d’entraînement, validation et test ;
  * garantir la **qualité, diversité et représentativité** ;
  * conserver la documentation pendant au moins 10 ans ;
  * assurer un **contrôle humain** sur les décisions critiques.

---

## 6. Outils et méthodes de cartographie pour l’IA

| Type d’outil           | Exemples                                    | Usage                                         |
| ---------------------- | ------------------------------------------- | --------------------------------------------- |
| **ETL / Data Catalog** | Talend Data Catalog, Collibra, Apache Atlas | Extraction, classification et métadonnées     |
| **Outils SIEM / ISO**  | Wazuh, Elastic, OpenAudit                   | Journalisation et suivi d’accès               |
| **Outils RGPD**        | OneTrust, Data Legal Drive, CNIL PIA        | Registre de traitements et AIPD               |
| **Outils IA / MLOps**  | MLflow, DataHub, Kubeflow                   | Traçabilité des datasets, versions et modèles |

**Bonnes pratiques :**

* Relier chaque source de données à sa finalité IA.
* Identifier les jeux utilisés pour chaque modèle.
* Documenter les transformations et nettoyages (scripts, paramètres).
* Stocker la documentation dans un **registre central de gouvernance**.

---

## 7. Travaux pratiques – Cartographie et conformité IA

### TP-1 : Créer une cartographie de flux IA

* Définir les sources : bases clients, API, fichiers logs, jeux open data.
* Identifier les flux : collecte → nettoyage → feature store → modèle → production.
* Déterminer les données sensibles et les points de contrôle (anonymisation, chiffrement).

### TP-2 : Construire un dictionnaire orienté IA

* Table : `dictionnaire_ia (source, colonne, type, sensibilité, usage_modele, responsable, statut_conformite)`
* Classer chaque champ selon RGPD : personnelle / technique / agrégée.
* Indiquer si la donnée est anonymisée ou pseudonymisée.

### TP-3 : Vérifier la conformité d’un jeu d’apprentissage

* Contrôler les doublons, biais, champs identifiants.
* Anonymiser les identifiants directs.
* Documenter le traitement et les règles de rétention.

### TP-4 : Produire un rapport de gouvernance

* Récapitulatif des sources et flux.
* Niveau de sensibilité.
* Conformité RGPD / ISO 27001 / AI Act.
* Recommandations (correctifs, sécurité, documentation).

---

## 8. Gouvernance intégrée et cycle de vie de la donnée IA

1. **Collecte** : respect du consentement, validation des sources.
2. **Nettoyage / préparation** : pseudonymisation et suppression des variables identifiantes.
3. **Stockage** : chiffrement AES-256, contrôle d’accès RBAC.
4. **Apprentissage** : documentation des datasets utilisés.
5. **Déploiement** : suivi de la dérive et des biais.
6. **Archivage / suppression** : politique de rétention conforme.

Chaque étape doit être **tracée et auditable**.
Le cycle complet forme la **chaîne de confiance des données IA**.

---

## 9. Indicateurs et reporting de conformité

| Indicateur                      | Description                                         | Seuil / Attente |
| ------------------------------- | --------------------------------------------------- | --------------- |
| % de données documentées        | Proportion des champs présents dans le dictionnaire | 100 %           |
| % de données classifiées        | Données dotées d’un niveau de sensibilité           | ≥ 95 %          |
| % de traitements RGPD conformes | Vérifiés via AIPD ou registre                       | ≥ 90 %          |
| % de datasets pseudonymisés     | Pour les jeux contenant des identifiants            | 100 %           |
| Temps de traçabilité            | Capacité à retracer l’origine d’un dataset          | < 4 h           |

---

## 10. Synthèse et recommandations

1. **Cartographier avant de collecter** : aucune IA fiable sans vision claire des données.
2. **Documenter chaque transformation** : l’IA doit pouvoir expliquer ses décisions.
3. **Classer et protéger les données** : appliquer chiffrement, anonymisation, accès restreint.
4. **Conformer les traitements** : RGPD (droits), ISO 27001 (sécurité), AI Act (transparence).
5. **Former les équipes** : culture de la donnée responsable et traçable.

---

## 11. Évaluation des compétences

| Domaine      | Compétence évaluée                                         | Niveau attendu |
| ------------ | ---------------------------------------------------------- | -------------- |
| Cartographie | Élaborer un schéma complet des flux et dépendances         | Avancé         |
| Gouvernance  | Mettre en place un dictionnaire et une classification RGPD | Avancé         |
| Sécurité     | Appliquer anonymisation, pseudonymisation et chiffrement   | Avancé         |
| Conformité   | Relier les pratiques aux exigences ISO 27001 et AI Act     | Expert         |
| Reporting    | Produire un rapport d’audit et de gouvernance IA           | Avancé         |

---

## 12. Projet final du module

**Sujet :**

> Concevoir la cartographie complète d’un projet IA (ex. modèle de recommandation), en y intégrant la documentation RGPD, la classification ISO, les traitements d’anonymisation et un rapport de conformité au AI Act.

**Livrables :**

1. Dictionnaire des données IA (SQL ou tableur).
2. Schéma de flux et de dépendances (diagramme ou outil).
3. Tableau de conformité RGPD / ISO 27001 / AI Act.
4. Rapport synthétique de gouvernance IA (Word ou PDF).

---

Souhaites-tu que je te **mette ce module au format Word** (mise en page professionnelle avec titres hiérarchisés, tableaux, encadrés pédagogiques et niveau de confidentialité ISO 27001) pour l’intégrer à ton plan de formation ?

J'ai bien analysé tous les fichiers fournis. Voici le compte-rendu de TP complet.

---

# Compte-rendu de TP — Gestion de véhicules

**Exercice :** TP SQL — Gestion de voitures et propriétaires

| Champ | Valeur |
|---|---|
| Étudiant | Waya CHEMLA |
| Base de données | xy_voitures |
| Module | Base de données — BTS SIO 1ère année |

---

## Phase 1 — Création de la base de données

```sql
create database xy_voitures ;
use xy_voitures ;
```

**Résultat attendu :**
```
Query OK, 1 row affected
Database changed
```

---

## Phase 2 — Création des tables

### Question 1 — Pourquoi pk_id et pk_immatriculation ?

**pk_id** : le préfixe `pk_` signifie "Primary Key". C'est la clé primaire de la table Personne. Elle identifie de façon unique chaque personne.

**pk_immatriculation** : même principe. L'immatriculation est unique pour chaque voiture, donc elle sert de clé primaire dans la table Voiture.

---

### Table Personne

```sql
CREATE TABLE Personne (
    pk_id int auto_increment,
    nom varchar(50),
    prenom varchar(50),
    code_postal int,
    ville varchar(50),
    email varchar(100),
    telephone varchar(20),
    PRIMARY KEY (pk_id)
);
```

**Résultat attendu :**
```
Query OK, 0 rows affected
```

---

### Table Voiture

```sql
CREATE TABLE Voiture (
    pk_immatriculation varchar(20),
    modele varchar(50),
    marque varchar(50),
    couleur varchar(30),
    prix float,
    proprietaire int,
    PRIMARY KEY (pk_immatriculation)
);
```

**Résultat attendu :**
```
Query OK, 0 rows affected
```

> **Note sur les types :** `pk_id` et `proprietaire` sont des entiers (`int`). `pk_immatriculation` est un `varchar` car une immatriculation contient des lettres et des chiffres. `email` et `telephone` sont des `varchar`. On peut vérifier les valeurs dans les fichiers INSERT pour confirmer les types.

---

## Phase 2 (suite) — Import des données

### Question 2 — Importer les données

On place les fichiers dans `C:\temp`, puis on les importe :

```sql
source c:/temp/SQL_insert_into_Personnes.txt;
source c:/temp/SQL_insert_into_Voitures.txt;
```

**Résultat attendu :**
```
Query OK, 1 row affected (x4 pour Personnes)
Query OK, 1 row affected (x6 pour Voitures)
```

### Vérification

```sql
SELECT * FROM Personne ;
SELECT * FROM Voiture ;
```

---

## Phase 3 — Questions et requêtes SQL

### Question 3 — Que voit-on dans la colonne email ?

```sql
SELECT * FROM Personne ;
```

**Résultat :** La colonne `email` affiche `NULL` pour toutes les personnes.

**Explication :** Les fichiers INSERT ne contenaient pas de valeur pour `email` et `telephone`. Quand on n't insère pas de valeur dans une colonne, MySQL met automatiquement `NULL`, ce qui signifie "valeur inconnue" ou "pas de valeur".

---

### Question 4 — Insérer Victor Hugo

```sql
INSERT INTO Personne (nom, prenom, code_postal, ville, email, telephone)
VALUES ('Hugo', 'Victor', 75003, 'Paris', 'v.hugo@paris.fr', '06 12 34 56 78') ;
```

**Quelle sera la valeur de pk_id ?**
La colonne `pk_id` est en `auto_increment`. Les données importées utilisent les pk_id 1, 2, 4 et 5. MySQL va donc attribuer automatiquement le prochain numéro disponible, soit **6** (MySQL ne réutilise pas les numéros manquants, il prend le max + 1).

**Est-on obligé de la préciser manuellement ?**
Non. Grâce à `auto_increment`, MySQL génère la valeur tout seul. On n'a pas besoin de l'écrire dans l'INSERT.

---

### Question 5 — Ajouter la Rolls Royce

```sql
INSERT INTO Voiture (pk_immatriculation, modele, marque, couleur)
VALUES ('GB987QE', 'Queen Elizabeth', 'Rolls Royce', 'Noir') ;
```

**Que mettre dans la colonne propriétaire ?**
Rien. On ne l'écrit tout simplement pas dans la liste des colonnes. MySQL mettra `NULL` automatiquement, ce qui signifie que cette voiture n'a pas encore de propriétaire.

---

### Question 6 — Insérer une Peugeot 208 non immatriculée

```sql
INSERT INTO Voiture (modele, marque, couleur, proprietaire)
VALUES ('208', 'Peugeot', 'Blanc', 5) ;
```

**Problème :** `pk_immatriculation` est la clé primaire et ne peut pas être `NULL`. Cette voiture n'est pas encore immatriculée, donc on ne peut pas l'insérer sans immatriculation.


**Pour la colonne propriétaire :** On indique le `pk_id` de Mme Boscolo, soit **5** (et non son nom). La colonne `proprietaire` contient un identifiant entier qui fait référence à `pk_id` de la table Personne.

---

### Question 7 — Insérer la Peugeot 2008 de M. Lepetit

**Dans quel ordre faire les choses ?** Il faut d'abord insérer M. Lepetit dans la table Personne pour avoir son `pk_id`, ensuite insérer la voiture en utilisant ce `pk_id`.

**Étape 1 — Insérer M. Lepetit :**

```sql
INSERT INTO Personne (nom, prenom, code_postal, ville, email, telephone)
VALUES ('Lepetit', 'Patrice', 75010, 'Paris', 'p.lepetit@turgot.fr', '06 11 22 33 44') ;
```

**Étape 2 — Retrouver son pk_id :**

```sql
SELECT * FROM Personne WHERE nom = 'Lepetit' ;
```

*(On suppose que son pk_id est 7.)*

**Étape 3 — Insérer la voiture :**

```sql
INSERT INTO Voiture (pk_immatriculation, modele, marque, proprietaire)
VALUES ('YT765ER', '2008', 'Peugeot', 7) ;
```

**Explication :** La couleur n'est pas précisée, donc on ne l'écrit pas. MySQL mettra `NULL`. On insère d'abord la personne car on a besoin de son `pk_id` pour remplir la colonne `proprietaire` de la voiture.

---

### Question 8 — Lister les prénoms et noms des propriétaires

```sql
SELECT prenom, nom
FROM Personne ;
```

**Résultat attendu :**

| prenom | nom |
|---|---|
| Stéphane | Crozat |
| Emmanuel | Bernier |
| Antoine | Vincent |
| Corinne | Boscolo |
| Victor | Hugo |
| Patrice | Lepetit |

---

### Question 9 — Lister les modèles de la marque Renault

```sql
SELECT modele
FROM Voiture
WHERE marque = 'Renault' ;
```

**Résultat attendu :**

| modele |
|---|
| Clio |
| Clio |
| Twingo |

---

### Question 10 — Lister les marques par ordre alphabétique

```sql
SELECT marque
FROM Voiture
ORDER BY marque ;
```

**Pour éviter les doublons :**

```sql
SELECT DISTINCT marque
FROM Voiture
ORDER BY marque ;
```

**Résultat attendu avec DISTINCT :**

| marque |
|---|
| Peugeot |
| Porsche |
| Renault |
| Rolls Royce |

**Explication :** `DISTINCT` permet de ne pas afficher deux fois la même marque. Sans `DISTINCT`, Renault et Peugeot apparaîtraient plusieurs fois.

---

### Question 11 — Lister les véhicules sans propriétaire

```sql
SELECT *
FROM Voiture
WHERE proprietaire IS NULL ;
```

**Résultat attendu :**

| pk_immatriculation | modele | marque | couleur | prix | proprietaire |
|---|---|---|---|---|---|
| AM007JB | 205 | Peugeot | Rose | NULL | NULL |
| GB987QE | Queen Elizabeth | Rolls Royce | Noir | NULL | NULL |

**Explication :** On utilise `IS NULL` et non `= NULL` pour tester si une valeur est vide. `= NULL` ne fonctionne pas en SQL.

---

### Question 12 — Nombre de véhicules

```sql
SELECT COUNT(*)
FROM Voiture ;
```

**Résultat attendu :**

| COUNT(*) |
|---|
| 8 |

---

### Question 13 — Nombre de véhicules de marque Renault

```sql
SELECT COUNT(*)
FROM Voiture
WHERE marque = 'Renault' ;
```

**Résultat attendu :**

| COUNT(*) |
|---|
| 3 |

---

### Question 14 — Personnes sans adresse e-mail

```sql
SELECT *
FROM Personne
WHERE email IS NULL ;
```

**Résultat attendu :** Les personnes importées depuis le fichier INSERT (Crozat, Bernier, Vincent, Boscolo) car elles n'avaient pas d'email saisi.

---

### Question 15 — Nombre de personnes sans adresse e-mail

```sql
SELECT COUNT(*)
FROM Personne
WHERE email IS NULL ;
```

**Résultat attendu :**

| COUNT(*) |
|---|
| 4 |

---

### Question 16 — Véhicules dont le modèle commence par la lettre C

```sql
SELECT *
FROM Voiture
WHERE modele LIKE 'C%' ;
```

**Résultat attendu :**

| pk_immatriculation | modele | marque | ... |
|---|---|---|---|
| AA123AA | Clio | Renault | ... |
| DE001TR | Clio | Renault | ... |
| BK200OB | Cayenne | Porsche | ... |

**Explication :** `LIKE 'C%'` signifie "commence par la lettre C". Le `%` remplace n'importe quelle suite de caractères.

---

### Questions difficiles

#### Lister les véhicules avec le nom du propriétaire

```sql
SELECT Voiture.*, Personne.nom
FROM Voiture, Personne
WHERE Voiture.proprietaire = Personne.pk_id ;
```

**Explication :** On utilise deux tables dans le `FROM`. La condition `WHERE` permet de faire le lien entre `proprietaire` (dans Voiture) et `pk_id` (dans Personne). Les véhicules sans propriétaire n'apparaissent pas.

---

#### Lister les véhicules avec le nom du propriétaire, pour ceux domiciliés à Paris

```sql
SELECT Voiture.*, Personne.nom
FROM Voiture, Personne
WHERE Voiture.proprietaire = Personne.pk_id
AND Personne.ville = 'Paris' ;
```

---

#### Compter le nombre de véhicules par marque

```sql
SELECT marque, COUNT(*)
FROM Voiture
GROUP BY marque ;
```

**Résultat attendu :**

| marque | COUNT(*) |
|---|---|
| Peugeot | 3 |
| Porsche | 1 |
| Renault | 3 |
| Rolls Royce | 1 |

---

#### Afficher les noms des propriétaires qui possèdent plus de deux véhicules

```sql
SELECT Personne.nom, COUNT(*)
FROM Voiture, Personne
WHERE Voiture.proprietaire = Personne.pk_id
GROUP BY Personne.nom
HAVING COUNT(*) > 2 ;
```

**Explication :** `HAVING` fonctionne comme un `WHERE` mais sur les résultats d'un `GROUP BY`. On ne peut pas utiliser `WHERE COUNT(*) > 2` directement.

---


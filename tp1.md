# Compte-rendu de TP — Création de tables SQL
## Exercice : Bibliothèque (sans clefs étrangères)

---

| | |
|---|---|
| **Étudiant** | Waya CHEMLA |
| **Base de données** | `wybibli` |
| **Date** | 26/06/2026 |
| **Module** | Base de données — BTS SIO 1ère année |

---

## Phase 1 — Création de la base de données

```sql
CREATE DATABASE wybibli;
USE wybibli;
```

---

## Phase 2 — Création des tables

### Table `auteurs`

```sql
CREATE TABLE auteurs (
  idauteur int auto_increment,
  nom varchar(40),
  prenom varchar(40),
  PRIMARY KEY (idauteur)
);
```

**Résultat attendu :**
```
Query OK, 0 rows affected (0.03 sec)
```

> La console affiche « 0 rows affected » car `CREATE TABLE` ne touche aucune ligne de données : elle crée uniquement la structure de la table.

**Import des données :**
```sql
source c:\temp\auteurs.sql;
```

**Résultat attendu :**
```
Query OK, 1 row affected (0.00 sec)
Query OK, 1 row affected (0.00 sec)
Query OK, 1 row affected (0.00 sec)
...
```

**Vérification :**
```sql
SELECT * FROM auteurs;
```

**Résultat attendu (extrait) :**
```
+-----------+---------------+---------+
| idauteur  | nom           | prenom  |
+-----------+---------------+---------+
| 1         | Spainhour     | Stephen |
| 2         | de Maupassant | Guy     |
| 3         | Jacq          | Christian |
| 6         | de Balzac     | Honoré  |
| ...       | ...           | ...     |
+-----------+---------------+---------+
```

> On remarque que pour « Honoré de Balzac », le nom est `de Balzac` et le prénom est `Honoré`. C'est cohérent avec les données du fichier `auteurs.sql` fourni.

---

### Table `livres`

```sql
CREATE TABLE livres (
  idlivre int auto_increment,
  isbn varchar(20),
  titre varchar(80),
  nbpages int(11),
  dateparu date,
  prix float,
  format varchar(20),
  PRIMARY KEY (idlivre)
);
```

> L'ISBN (International Serial Book Number) est stocké en `varchar` et non en `int`, car il contient des tirets et ne sert pas à faire des calculs.

**Import des données :**
```sql
source c:\temp\livres.sql;
```

**Vérification :**
```sql
SELECT * FROM livres;
```

---

### Table `abonnes`

```sql
CREATE TABLE abonnes (
  idemprunteur int auto_increment,
  nom varchar(30),
  prenom varchar(30),
  adresse varchar(50),
  code varchar(5),
  ville varchar(50),
  tel varchar(20),
  sexe varchar(2),
  datenaiss date,
  nbretards int(11),
  PRIMARY KEY (idemprunteur)
);
```

**Import des données :**
```sql
source c:\temp\abonnes.sql;
```

**Vérification :**
```sql
SELECT * FROM abonnes;
```

---

### Table `editeurs`

```sql
CREATE TABLE editeurs (
  idediteur int(11) auto_increment,
  nom varchar(40),
  adresse varchar(60),
  code char(5),
  ville varchar(40),
  pays varchar(50),
  tel varchar(20),
  fax varchar(20),
  PRIMARY KEY (idediteur)
);
```

**Import des données :**
```sql
source c:\temp\editeurs.sql;
```

**Import du correctif :**
```sql
source c:\temp\editeurs_correctif.sql;
```

**Vérification :**
```sql
SELECT * FROM editeurs;
```

---

## Phase 3 — Extraction de données (SELECT)

### Lister les noms des abonnés

```sql
SELECT nom FROM abonnes;
```

---

### Lister les noms et prénoms des abonnés

```sql
SELECT nom, prenom FROM abonnes;
```

---

### Lister les noms et numéros de téléphone des éditeurs

```sql
SELECT nom, tel FROM editeurs;
```

---

### Lister les abonnés qui habitent à Nice

```sql
SELECT nom, ville FROM abonnes WHERE ville = 'Nice';
```

> Si on écrit `WHERE ville = 'NICE'` en majuscules, MySQL ne retourne aucun résultat car la valeur stockée est `'Nice'` avec une majuscule uniquement sur la première lettre.

---

### Lister les éditeurs qui viennent de Nice

```sql
SELECT nom FROM editeurs WHERE ville = 'Nice';
```

**Résultat attendu :**
```
+--------------------+
| nom                |
+--------------------+
| Editions du Cocher |
+--------------------+
1 row in set
```

---

### Lister les noms et prénoms des auteurs dont le prénom est Jean

```sql
SELECT nom, prenom FROM auteurs WHERE prenom = 'Jean';
```

**Résultat attendu :**
```
+---------+--------+
| nom     | prenom |
+---------+--------+
| Cocteau | Jean   |
| Anouilh | Jean   |
+---------+--------+
2 rows in set
```

---

### Lister les titres des livres dont le format est « Poche »

```sql
SELECT titre FROM livres WHERE format = 'Poche';
```

---

### Lister les formats des livres

**Sans filtre (avec doublons) :**
```sql
SELECT format FROM livres;
```

**Sans doublons avec `DISTINCT` :**
```sql
SELECT DISTINCT format FROM livres;
```

**Résultat attendu :**
```
+--------------+
| format       |
+--------------+
| Poche        |
| Petit format |
| Demi format  |
| Grand format |
| BD           |
| Demi_format  |
+--------------+
6 rows in set
```

---

### Lister les livres dont l'auteur est « Honoré de Balzac »

> La table `livres` ne contient pas de colonne `idauteur` dans cette phase du TP (pas encore de clés étrangères). Il n'est donc pas possible de faire une jointure entre `livres` et `auteurs`. On peut uniquement retrouver l'auteur dans la table `auteurs` :

```sql
SELECT nom, prenom FROM auteurs WHERE nom = 'de Balzac';
```

**Résultat attendu :**
```
+-----------+--------+
| nom       | prenom |
+-----------+--------+
| de Balzac | Honoré |
+-----------+--------+
1 row in set
```

---

*Fin du compte-rendu de TP*

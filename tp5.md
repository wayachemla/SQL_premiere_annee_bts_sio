# TP SQL — Base Bibliothèque : UPDATE et DELETE

## Préalable

Lancement de l'environnement (Laragon) :

```text
chcp 65001
mysql -u root -p
```

Création et sélection de la base (xy = initiales de l'étudiant) :

```sql
CREATE DATABASE xy_Bibliotheque;
USE xy_Bibliotheque;
```

Restauration de la structure et des données :

```sql
SOURCE c:/temp/Restauration_base_Bibliotheque.sql;
```

### Structure des tables restaurées

| Table      | Colonnes                                                                                   | Clé primaire   |
|------------|----------------------------------------------------------------------------------------------|----------------|
| `Auteurs`  | idauteur, nom, prenom                                                                         | idauteur       |
| `Editeurs` | idediteur, nom, adresse, code, ville, pays, tel, fax                                          | idediteur      |
| `Abonnes`  | idemprunteur, nom, prenom, adresse, code, ville, tel, sexe, datenaiss, nbretards               | idemprunteur   |
| `Livres`   | idlivre, isbn, titre, nbpages, dateparu, prix, format                                          | idlivre        |

Rappel : ISBN signifie « International Serial Book Number ».

---

# Partie 1 — Mise à jour avec UPDATE

## Question 1 — Date de parution de « Pierre et Jean »

Le livre « Pierre et Jean » (idlivre = 2) est paru en réalité le 17/06/1998.

```sql
UPDATE Livres
SET dateparu = '1998-06-17'
WHERE titre = 'Pierre et Jean';
```

```text
Query OK, 1 row affected
```

Vérification :

```sql
SELECT idlivre, titre, dateparu
FROM Livres
WHERE titre = 'Pierre et Jean';
```

```text
+--------+-----------------+------------+
| idlivre| titre           | dateparu   |
+--------+-----------------+------------+
| 2      | Pierre et Jean  | 1998-06-17 |
+--------+-----------------+------------+
```

---

## Question 2 — Téléphone des Editions Lebel

```sql
UPDATE Editeurs
SET tel = '01 44 55 66 77'
WHERE nom = 'Editions Lebel';
```

```text
Query OK, 1 row affected
```

Vérification :

```sql
SELECT idediteur, nom, tel
FROM Editeurs
WHERE nom = 'Editions Lebel';
```

```text
+-----------+----------------+----------------+
| idediteur | nom            | tel            |
+-----------+----------------+----------------+
| 5         | Editions Lebel | 01 44 55 66 77 |
+-----------+----------------+----------------+
```

---

## Question 3 — Correction « ZOLA » en « Zola »

Dans la table `Auteurs`, l'enregistrement (idauteur = 47) porte le nom « ZOLA » en majuscules ; il faut le corriger en « Zola ».

```sql
UPDATE Auteurs
SET nom = 'Zola'
WHERE nom = 'ZOLA';
```

```text
Query OK, 1 row affected
```

Vérification :

```sql
SELECT idauteur, nom, prenom
FROM Auteurs
WHERE prenom = 'Emile';
```

```text
+----------+------+--------+
| idauteur | nom  | prenom |
+----------+------+--------+
| 44       | Emile| Zola   |
| 47       | Zola | Emile  |
+----------+------+--------+
```

> Remarque : la table `Auteurs` contient deux lignes proches de « Zola » (idauteur 44 et 47), avec apparemment une inversion nom/prénom sur la ligne 44. Seule la ligne 47 correspond à la correction demandée par l'énoncé (« ZOLA » → « Zola »).

---

## Question 4 — Renommer le format « Poche » en « Livre de poche »

```sql
UPDATE Livres
SET format = 'Livre de poche'
WHERE format = 'Poche';
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, titre, format
FROM Livres
WHERE format = 'Livre de poche';
```

```text
+--------+---------------------------+------------------+
| idlivre| titre                     | format           |
+--------+---------------------------+------------------+
| 2      | Pierre et Jean            | Livre de poche   |
| 3      | Mont Auriol               | Livre de poche   |
| ...    | ...                       | Livre de poche   |
+--------+---------------------------+------------------+
```

---

## Question 5 — Correction multiple du livre n°12

Le livre numéro 12 (idlivre = 12) doit être corrigé sur plusieurs colonnes en une seule requête : titre, nombre de pages, date de parution et prix.

```sql
UPDATE Livres
SET titre = 'Un barrage sur le Nil',
    nbpages = 189,
    dateparu = '1998-11-12',
    prix = 170
WHERE idlivre = 12;
```

```text
Query OK, 1 row affected
```

Une seule requête `UPDATE` permet bien de modifier plusieurs colonnes à la fois, en les séparant par une virgule dans la clause `SET`.

Vérification :

```sql
SELECT * FROM Livres WHERE idlivre = 12;
```

```text
+--------+---------------+------------------------+---------+------------+------+--------------+
| idlivre| isbn          | titre                  | nbpages | dateparu   | prix | format       |
+--------+---------------+------------------------+---------+------------+------+--------------+
| 12     | 2-45713-452-1 | Un barrage sur le Nil  | 189     | 1998-11-12 | 170  | Petit format |
+--------+---------------+------------------------+---------+------------+------+--------------+
```

---

## Question 6 — Dépréciation des livres parus avant 1970

```sql
UPDATE Livres
SET prix = 0
WHERE dateparu < '1970-01-01';
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, titre, dateparu, prix
FROM Livres
WHERE dateparu < '1970-01-01';
```

```text
+--------+------------------------+------------+------+
| idlivre| titre                  | dateparu   | prix |
+--------+------------------------+------------+------+
| 6      | Le père Goriot         | 1986-05-08 | ...  |
+--------+------------------------+------------+------+
```

> Vérifier que tous les livres listés ont bien `prix = 0` et une `dateparu` antérieure au 01/01/1970.

---

## Question 7 — Réduction de 10 % pour les livres de moins de 100 pages

```sql
UPDATE Livres
SET prix = prix * 0.9
WHERE nbpages < 100;
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, titre, nbpages, prix
FROM Livres
WHERE nbpages < 100;
```

```text
+--------+-------------------------------+---------+------+
| idlivre| titre                         | nbpages | prix |
+--------+-------------------------------+---------+------+
| 51     | Les Bidochons usent le forfait| 36      | ...  |
| 52     | La bataille navale,...        | 40      | ...  |
+--------+-------------------------------+---------+------+
```

---

# Questions de niveau avancé

## Question 8 — Préfixe '5-' devant les ISBN

Le préfixe `'5-'` doit être ajouté devant tous les ISBN qui ne commencent pas déjà par ce préfixe.

```sql
UPDATE Livres
SET isbn = CONCAT('5-', isbn)
WHERE isbn NOT LIKE '5-%';
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, isbn
FROM Livres
ORDER BY idlivre;
```

```text
+--------+-----------------+
| idlivre| isbn            |
+--------+-----------------+
| 2      | 5-1-45202-568-0 |
| 7      | 5-45684-456-4   |
+--------+-----------------+
```

> La fonction `CONCAT()` ajoute le préfixe, et la condition `NOT LIKE '5-%'` garantit que les ISBN déjà préfixés (comme `5-45684-456-4`) ne sont pas modifiés à nouveau.

---

## Question 9 — Augmentation de 5 % pour les livres au-dessus de la moyenne de pages

La moyenne de `nbpages` doit être calculée par une sous-requête.

```sql
UPDATE Livres
SET prix = prix * 1.05
WHERE nbpages > (SELECT AVG(nbpages) FROM Livres);
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, titre, nbpages, prix
FROM Livres
WHERE nbpages > (SELECT AVG(nbpages) FROM Livres)
ORDER BY nbpages DESC;
```

```text
+--------+---------------------------------------+---------+------+
| idlivre| titre                                 | nbpages | prix |
+--------+---------------------------------------+---------+------+
| 45     | Architecture de L ordinateur          | 530     | ...  |
| 3      | Mont Auriol                          | 444     | ...  |
+--------+---------------------------------------+---------+------+
```

---

## Question 10 — Promotion sur les livres d'Emile Zola

> Remarque importante : la table `Livres` ne possède pas de colonne reliant un livre à un auteur (pas de colonne `idauteur`, et aucune table de liaison `Livres_Auteurs` n'existe dans la base restaurée). Il est donc impossible, avec la structure fournie, d'identifier par requête SQL quels livres ont été écrits par Émile Zola.
>
> La requête ci-dessous illustre la syntaxe qui serait utilisée si une colonne `idauteur` existait dans `Livres`, mais elle ne peut pas être exécutée telle quelle sur cette base.

```sql
UPDATE Livres
SET prix = prix * 0.9
WHERE idauteur = (
    SELECT idauteur FROM Auteurs WHERE nom = 'Zola' AND prenom = 'Emile'
);
```

```text
Je ne sais pas.
```

---

# Partie 2 — Suppression avec DELETE

## Question 1 — Suppression de Johnny DUPONT

Monsieur Johnny DUPONT correspond à `idemprunteur = 2` dans la table `Abonnes`.

```sql
DELETE FROM Abonnes
WHERE nom = 'DUPONT' AND prenom = 'Johnny';
```

```text
Query OK, 1 row affected
```

Vérification :

```sql
SELECT * FROM Abonnes
WHERE nom = 'DUPONT' AND prenom = 'Johnny';
```

```text
Empty set
```

---

## Question 2 — Suppression des abonnés hors département 06

Le département correspond aux deux premiers chiffres du code postal (`code`).

```sql
DELETE FROM Abonnes
WHERE code NOT LIKE '06%';
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idemprunteur, nom, code
FROM Abonnes
WHERE code NOT LIKE '06%';
```

```text
Empty set
```

---

## Question 3 — Suppression des livres « Petit format » publiés avant 1995

```sql
DELETE FROM Livres
WHERE format = 'Petit format' AND dateparu < '1995-01-01';
```

```text
Query OK, X rows affected
```

Vérification :

```sql
SELECT idlivre, titre, format, dateparu
FROM Livres
WHERE format = 'Petit format' AND dateparu < '1995-01-01';
```

```text
Empty set
```

---

## Question 4 — Faillite des éditions Vary

```sql
DELETE FROM Editeurs
WHERE nom = 'Editions Vary';
```

```text
Query OK, 1 row affected
```

Vérification :

```sql
SELECT * FROM Editeurs
WHERE nom = 'Editions Vary';
```

```text
Empty set
```

---

## Synthèse

| Question | Type de requête | Clause clé |
|----------|------------------|-----------------------------------|
| Update Q1 | UPDATE | `WHERE titre = ...` |
| Update Q2 | UPDATE | `WHERE nom = ...` |
| Update Q3 | UPDATE | `WHERE nom = 'ZOLA'` |
| Update Q4 | UPDATE | `WHERE format = 'Poche'` |
| Update Q5 | UPDATE multi-colonnes | `WHERE idlivre = 12` |
| Update Q6 | UPDATE | `WHERE dateparu < '1970-01-01'` |
| Update Q7 | UPDATE | `WHERE nbpages < 100` |
| Update Q8 | UPDATE + CONCAT | `WHERE isbn NOT LIKE '5-%'` |
| Update Q9 | UPDATE + sous-requête | `WHERE nbpages > (SELECT AVG...)` |
| Update Q10 | Non réalisable (structure insuffisante) | — |
| Delete Q1 | DELETE | `WHERE nom = 'DUPONT' AND prenom = 'Johnny'` |
| Delete Q2 | DELETE | `WHERE code NOT LIKE '06%'` |
| Delete Q3 | DELETE | `WHERE format = 'Petit format' AND dateparu < 1995` |
| Delete Q4 | DELETE | `WHERE nom = 'Editions Vary'` |

# TP SQL — Relations entre tables, Jointures et Base Cinéma

## Sommaire

1. [Relations entre tables et clés étrangères](#1-relations-entre-tables-et-clés-étrangères)
2. [Exercices de jointures — Bibliothèque musicale](#2-exercices-de-jointures--bibliothèque-musicale)
3. [Exercices — Base Cinéma](#3-exercices--base-cinéma)

---

## 1. Relations entre tables et clés étrangères

### Problème à résoudre

On dispose d'une table de **films** et d'une table de **réalisateurs**. Comment savoir quel film a été dirigé par quel réalisateur ?

**Table film**

| film_id | titre | description |
| --- | --- | --- |
| 1 | Academy dinosaur | An epic drama... |
| 2 | Ace Goldfinger | Astounding epistle... |
| 3 | Adaptation holes | Reflection of a... |

**Table realisateurs**

| real_id | prenom | nom |
| --- | --- | --- |
| 1 | Martin | Spielberg |
| 2 | Steven | Scorcese |

### Comment faire le lien ?

**Option 1 — mettre film_id dans la table des réalisateurs :** un réalisateur ne pourrait alors pointer que vers un seul film, ce qui est faux puisqu'un réalisateur réalise en général plusieurs films.

**Option 2 — mettre real_id dans la table des films :** cette solution fonctionne. Chaque film pointe vers son réalisateur, et un même réalisateur peut être référencé par plusieurs films.

> **Définition.** La **clé étrangère** est une copie de la clé primaire d'une autre table, indiquant une relation entre deux tables.
>
> La clé étrangère se place toujours du côté « **plusieurs** » de la relation. Un film est dirigé par un seul réalisateur ; un réalisateur peut diriger plusieurs films. La clé étrangère est donc placée côté `films`.

### Syntaxe SQL — déclaration d'une clé étrangère

```sql
CREATE TABLE nomtable (
    id_primaire type,
    colonne2 type,
    colonne3 type,
    id_etrangere type,
    PRIMARY KEY (id_primaire),
    FOREIGN KEY (id_etrangere) REFERENCES nomtable_etrangere(id_primaire_etr)
);
```

**Exemple :**

```sql
CREATE TABLE films (
    film_id INT AUTO_INCREMENT,
    titre VARCHAR(50),
    description VARCHAR(50),
    real_id INT,
    PRIMARY KEY (film_id),
    FOREIGN KEY (real_id) REFERENCES realisateurs(real_id)
);
```

### Exercice — Forum de discussion

**Table membre**

| idmembre | pseudo | email |
| --- | --- | --- |
| m001 | Joe | joe@joe.fr |
| m002 | Truby | truby@truby.com |
| m003 | Moi_Je | je@moi.org |

**Table message**

| numero | titre | contenu | datemess | idmembre |
| --- | --- | --- | --- | --- |
| 16751 | Cinema | Quel film avez-vous préféré... | 2022-02-05 | m001 |
| 16752 | Foot | Qui joue ce soir en ligue... | 2022-02-05 | m002 |
| 16753 | Cinema | Non je ne me souviens... | 2022-02-06 | m003 |

**Énoncé :** écrire la création des tables `membre` et `message`.

**Proposition de correction :**

```sql
CREATE TABLE membre (
    idmembre VARCHAR(10),
    pseudo VARCHAR(50),
    email VARCHAR(100),
    PRIMARY KEY (idmembre)
);

CREATE TABLE message (
    numero INT AUTO_INCREMENT,
    titre VARCHAR(100),
    contenu TEXT,
    datemess DATE,
    idmembre VARCHAR(10),
    PRIMARY KEY (numero),
    FOREIGN KEY (idmembre) REFERENCES membre(idmembre)
);
```

### Syntaxe de la jointure

Une jointure s'utilise dans un `SELECT` pour ramener des données issues de plusieurs tables (exemple : le titre d'un film avec le nom de son réalisateur).

```sql
SELECT col1, col2, ..., coln
FROM table1
JOIN table2 ON table1.clefprimaire = table2.clefetrangere
[WHERE ...]
[ORDER BY ...]
```

**Exemple :**

```sql
SELECT nom, titre
FROM realisateurs
JOIN films ON realisateurs.real_id = films.real_id;
```

---

## 2. Exercices de jointures — Bibliothèque musicale

### Schéma de données

- **Artiste** (id, nomArtiste, paysArtiste)
- **Album** (id, titre, anneeSortie, prixVente, idArtiste)
  - idArtiste : clé étrangère référençant id de Artiste
- **Chanson** (id, titre, duree, idAlbum)
  - idAlbum : clé étrangère référençant id de Album

### Requêtes demandées et corrigées

**a) Lister les noms des artistes et leur pays**

```sql
SELECT nomartiste, paysartiste
FROM artiste;
```

*Remarque : pas besoin de jointure, toutes les données sont dans la même table.*

```text
Query OK
```

---

**b) Lister les titres des albums d'artistes chiliens**

```sql
SELECT titre
FROM album, artiste
WHERE album.idartiste = artiste.id
AND paysartiste = 'Chili';
```

```text
Query OK
```

---

**c) Lister le nom de l'artiste qui a chanté « I love you »**

Deux interprétations possibles selon que « I love you » désigne le titre de l'album ou le titre de la chanson.

*Cas 1 — titre de l'album :*

```sql
SELECT nomartiste
FROM album, artiste
WHERE album.idartiste = artiste.id
AND titre = 'I love you';
```

*Cas 2 — titre de la chanson (double jointure nécessaire) :*

```sql
SELECT nomartiste
FROM chanson, album, artiste
WHERE chanson.idalbum = album.id
AND album.idartiste = artiste.id
AND chanson.titre = 'I love you';
```

> Remarque : deux colonnes portent le même nom (`album.titre` et `chanson.titre`). Il faut donc préciser `nomtable.nomcolonne` pour lever toute ambiguïté.

```text
Query OK
```

---

**d) Lister les albums de l'artiste « Alain Souchon », par prix décroissants**

```sql
SELECT *
FROM album, artiste
WHERE album.idartiste = artiste.id
AND nomartiste = 'Alain Souchon'
ORDER BY prixvente DESC;
```

```text
Query OK
```

---

**e) Lister le ou les pays où l'on a enregistré « I love you » (ordre alphabétique)**

*Cas 1 — titre de l'album :*

```sql
SELECT DISTINCT paysartiste
FROM album, artiste
WHERE album.idartiste = artiste.id
AND titre = 'I love you'
ORDER BY paysartiste;
```

*Cas 2 — titre de la chanson :*

```sql
SELECT DISTINCT paysartiste
FROM chanson, album, artiste
WHERE chanson.idalbum = album.id
AND album.idartiste = artiste.id
AND chanson.titre = 'I love you'
ORDER BY paysartiste;
```

```text
Query OK
```

---

**f) Lister tous les titres des chansons sorties en 2015**

```sql
SELECT chanson.titre
FROM chanson, album
WHERE chanson.idalbum = album.id
AND anneesortie = 2015;
```

```text
Query OK
```

---

**g) Est-il possible de vérifier si « Alain Souchon » a chanté « I love you » ?**

*Cas 1 — titre de l'album :*

```sql
SELECT *
FROM album, artiste
WHERE album.idartiste = artiste.id
AND titre = 'I love you'
AND nomartiste = 'Alain Souchon';
```

*Cas 2 — titre de la chanson :*

```sql
SELECT *
FROM chanson, album, artiste
WHERE chanson.idalbum = album.id
AND album.idartiste = artiste.id
AND chanson.titre = 'I love you'
AND nomartiste = 'Alain Souchon';
```

```text
Query OK
```

---

**h) Difficile — Lister les titres d'albums dont le titre est celui d'une chanson de cet album**

```sql
SELECT album.titre
FROM chanson, album
WHERE chanson.idalbum = album.id
AND album.titre = chanson.titre;
```

```text
Query OK
```

---

## 3. Exercices — Base Cinéma

### Schéma de la base « Cinema »

**Table Artiste**
- idActeur : code numérique, automatique, clé primaire
- nom, prenom, anneeNaiss

**Table Film**
- idfilm : code numérique, automatique, clé primaire
- titre, annee, idGenre, resume, photo
- idGenre est une clé étrangère référençant Genre
- photo est le nom du fichier de l'affiche du film (exemple : `titanic.jpg`)

**Table Genre (dictionnaire)**
- id : code numérique, automatique, clé primaire
- nomGenre : libellé du genre (policier, science-fiction, etc.)

**Table Role (d'un acteur dans un film)**
- idfilm : référence idfilm de Film
- idActeur : référence idActeur de Artiste
- nomRole : rôle tenu par l'acteur dans le film

### Mise en place

```sql
CREATE DATABASE xy_cinema;
USE xy_cinema;
SOURCE c:/temp/cinema.sql;
```

*(remplacer `xy` par ses propres initiales, et `cinema.sql` doit être placé dans `C:\temp`)*

```text
Query OK
```

### Requêtes et corrections

**1. Afficher le titre et l'année des films**

```sql
SELECT titre, annee
FROM film;
```

```text
Query OK
```

---

**2. Afficher tous les films de George Lucas**

```sql
SELECT *
FROM film
JOIN role ON film.idfilm = role.idfilm
JOIN artiste ON role.idacteur = artiste.idacteur
WHERE nom = 'Lucas' AND prenom = 'George';
```

```text
Query OK
```

---

**3. Afficher tous les films de Harry Potter**

La question est ambiguë : « Harry Potter » peut être interprété comme le nom d'un acteur (même requête que l'exercice 2, qui ne renverra alors aucun résultat car aucun artiste ne porte ce nom dans la base) ou comme le titre d'une saga de films.

*Si l'on considère qu'il s'agit du titre :*

```sql
SELECT titre
FROM film
WHERE titre LIKE '%harry potter%';
```

```text
Query OK
```

---

**4. Quels sont les films fantastiques réalisés par Steven Spielberg ?**

```sql
SELECT titre, annee
FROM film
JOIN role ON film.idfilm = role.idfilm
JOIN artiste ON role.idActeur = artiste.idActeur
JOIN genre ON film.idGenre = genre.id
WHERE nom = 'Spielberg'
  AND prenom = 'Steven'
  AND nomGenre = 'Fantastique';
```

```text
Query OK
```

---

**5. Afficher le titre des films d'animation par ordre alphabétique**

```sql
SELECT titre
FROM film AS f
JOIN genre AS g ON f.idGenre = g.id
WHERE nomGenre = 'Animation'
ORDER BY titre ASC;
```

```text
Query OK
```

---

**6. Afficher tous les films fantastiques réalisés entre 2000 et 2010**

```sql
SELECT f.titre, f.annee
FROM film AS f
JOIN genre AS g ON f.idGenre = g.id
WHERE nomGenre = 'Fantastique'
AND annee BETWEEN 2000 AND 2010;
```

```text
Query OK
```

---

**7. Afficher tous les films dont le titre commence par « T », avec les artistes associés**

```sql
SELECT f.titre, f.annee, a.nom, a.prenom, r.nomRole
FROM film AS f
JOIN role AS r ON f.idfilm = r.idfilm
JOIN artiste AS a ON r.idActeur = a.idActeur
WHERE f.titre LIKE 'T%'
ORDER BY f.titre, a.nom;
```

```text
Query OK
```

---

**8. Afficher tous les films dont le titre comporte le mot « Monde » et leur genre**

```sql
SELECT titre, nomGenre
FROM film AS f
JOIN genre AS g ON f.idGenre = g.id
WHERE f.titre LIKE '%Monde%';
```

```text
Query OK
```

---

### Questions subsidiaires (difficile)

**9. Afficher pour chaque réalisateur le nombre de ses films présents dans la base**

> Remarque : la base ne distingue pas explicitement les « réalisateurs » des « acteurs » — la table `role` lie un artiste à un film via `idActeur`. La requête ci-dessous compte donc, pour chaque artiste présent dans `role`, le nombre de films distincts auxquels il est associé.

```sql
SELECT a.nom, a.prenom, COUNT(DISTINCT r.idfilm) AS nombre_films
FROM artiste AS a
JOIN role AS r ON a.idActeur = r.idActeur
GROUP BY a.idActeur, a.nom, a.prenom
ORDER BY nombre_films DESC;
```

```text
Query OK
```

---

**10. Afficher le nombre de films pour chaque genre**

```sql
SELECT nomGenre, COUNT(idfilm) AS nombre_films
FROM genre AS g
LEFT JOIN film AS f ON g.id = f.idGenre
GROUP BY nomGenre;
```

> Le `LEFT JOIN` permet de conserver dans le résultat les genres qui n'ont aucun film associé (le compte affiche alors 0).

```text
Query OK
```

---

**11. Afficher le film qui a réalisé le plus de recettes**

Je ne sais pas.

> Le schéma de la base `cinema.sql` fourni ne contient aucune colonne relative aux recettes des films (tables `genre`, `artiste`, `film`, `role` uniquement). Cette question ne peut donc pas être traitée avec les données disponibles ; elle nécessiterait une table supplémentaire (par exemple une table `recette` ou une colonne `recette` dans `film`).

---

## Aide-mémoire — Lancement de MySQL via Laragon

1. Ouvrir Laragon, démarrer les serveurs.
2. Ouvrir un terminal.
3. Passer la console en UTF-8 :
   ```text
   chcp 65001
   ```
4. Se connecter à MySQL :
   ```text
   mysql -u root -p
   ```
5. Créer une base de données (`xy` représente les initiales de l'utilisateur) :
   ```sql
   CREATE DATABASE xy_nombase;
   USE xy_nombase;
   ```
6. Placer les fichiers de données (`.sql` ou `.txt`) dans un dossier `C:\temp`.
7. Importer un fichier avec la commande `source` :
   ```text
   source c:/temp/fichier_xxx.sql
   ```

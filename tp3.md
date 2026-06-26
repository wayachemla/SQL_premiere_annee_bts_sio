# Compte-rendu de TP — TP SQL – SELECT

## Exercice : Base de données « Cinéma »

| Étudiant | Waya CHEMLA |
|---|---|
| Base de données | cinema (ex : vh_cinema) |
| Module | Base de données — BTS SIO 1ère année |

---

## Phase 1 — Création de la base de données

On se connecte à MySQL via le terminal Laragon, puis on crée la base de données en remplaçant `vh` par ses propres initiales :

```sql
create database vh_cinema;
use vh_cinema;
```

**Résultat attendu :**
```
Query OK, 1 row affected
Database changed
```

---

## Phase 2 — Création des tables

### Table `Artiste`

En observant le fichier de données `Données_Artiste.sql`, on voit que chaque artiste a : un identifiant entier, un nom (texte court), un prénom (texte court), et une année de naissance (entier, parfois absent = NULL).

On s'appuie aussi sur le modèle de création de table fourni.

```sql
DROP TABLE IF EXISTS Artiste;
CREATE TABLE Artiste (
  idActeur int PRIMARY KEY auto_increment,
  nom varchar(30),
  prenom varchar(30),
  anneeNaiss int
);
```

**Résultat attendu :**
```
Query OK, 0 rows affected
```

### Table `Film`

La structure de la table Film est fournie directement dans l'énoncé :

```sql
CREATE TABLE Film (
  idfilm int PRIMARY KEY auto_increment,
  titre varchar(50),
  annee int,
  idGenre int,
  resume text,
  photo varchar(50)
);
```

**Résultat attendu :**
```
Query OK, 0 rows affected
```

---

## Import des données

On place les fichiers `.sql` dans `C:\temp` puis on les importe avec la commande `source`.

```sql
source c:/temp/Données_Artiste.sql;
source c:/temp/Films.sql;
```

**Résultat attendu :**
```
Query OK, plusieurs lignes affectées
```

### Vérification

```sql
SELECT * FROM Artiste;
SELECT * FROM Film;
```

---

## Phase 3 — Requêtes SQL sur la table `Artiste`

---

### Requête 1 — Afficher toutes les données des artistes

```sql
SELECT * FROM Artiste;
```

**Résultat attendu :**
Toutes les lignes de la table Artiste s'affichent (idActeur, nom, prenom, anneeNaiss).

**Explication courte :**
Le `*` veut dire "toutes les colonnes". On affiche tout le contenu de la table.

---

### Requête 2 — Afficher uniquement les noms et prénoms des artistes

```sql
SELECT nom, prenom FROM Artiste;
```

**Résultat attendu :**
Une liste avec seulement les colonnes nom et prenom.

**Explication courte :**
On remplace `*` par les noms des colonnes qu'on veut afficher, séparés par une virgule.

---

### Requête 3 — Afficher les noms et prénoms, triés par nom croissant

```sql
SELECT nom, prenom FROM Artiste ORDER BY nom ASC;
```

**Résultat attendu :**
La liste des artistes triée de A à Z selon le nom.

**Explication courte :**
`ORDER BY nom ASC` trie les résultats par la colonne `nom` dans l'ordre croissant (A, B, C...).

---

### Requête 4 — Afficher les artistes nés après 1990

```sql
SELECT * FROM Artiste WHERE anneeNaiss > 1990;
```

**Résultat attendu :**
Seuls les artistes dont `anneeNaiss` est supérieure à 1990 apparaissent. Exemple : Christina Ricci (1980 est exclu, mais pas 1990 strict).

**Explication courte :**
`WHERE` permet de filtrer. `> 1990` garde uniquement les lignes où l'année est strictement supérieure à 1990.

---

### Requête 5 — Afficher les artistes nés entre 1980 et 2000

```sql
SELECT * FROM Artiste WHERE anneeNaiss BETWEEN 1980 AND 2000;
```

**Résultat attendu :**
Les artistes dont l'année de naissance est comprise entre 1980 et 2000 inclus.

**Explication courte :**
`BETWEEN ... AND ...` inclut les deux bornes. C'est plus lisible qu'écrire `>= 1980 AND <= 2000`.

---

### Requête 6 — Même question, triée du plus jeune au plus âgé (Christina Ricci en premier)

```sql
SELECT * FROM Artiste WHERE anneeNaiss BETWEEN 1980 AND 2000 ORDER BY anneeNaiss DESC;
```

**Résultat attendu :**
La liste triée par année décroissante : les artistes nés le plus récemment apparaissent en premier. Christina Ricci (née en 1980) doit apparaître en premier si elle est la plus récente dans cet intervalle.

> Note : Christina Ricci est née en 1980. En triant par `anneeNaiss DESC`, les années les plus grandes (les plus récentes) apparaissent en premier. Si elle est la seule née en 1980, elle sera en dernière position avec DESC. En regardant les données, Milla Jovovich (1975), Kate Winslet (1975), Leonardo DiCaprio (1974)... Ricci (1980) est bien la plus jeune, donc elle apparaît en premier avec ORDER BY anneeNaiss DESC.

**Explication courte :**
`ORDER BY anneeNaiss DESC` trie du plus grand au plus petit. Donc le plus grand numéro d'année = la personne la plus jeune = apparaît en premier.

---

### Requête 7 — Artistes dont le nom commence par "D"

```sql
SELECT * FROM Artiste WHERE nom LIKE 'D%';
```

**Résultat attendu :**
Les artistes dont le nom commence par la lettre D (ex : Depp, Diaz, Dillon, Dutronc...).

**Explication courte :**
`LIKE` sert à chercher un motif dans du texte. Le `%` remplace n'importe quelle suite de caractères. `'D%'` signifie "commence par D".

---

### Requête 8 — Artistes dont le nom contient "son"

```sql
SELECT * FROM Artiste WHERE nom LIKE '%son%';
```

**Résultat attendu :**
Les artistes dont le nom contient "son" (ex : Jackson).

**Explication courte :**
Les deux `%` autour de "son" signifient qu'il peut y avoir des caractères avant et après. On cherche "son" n'importe où dans le nom.

---

### Requête 9 — Artistes dont l'année de naissance est inconnue

```sql
SELECT * FROM Artiste WHERE anneeNaiss IS NULL;
```

**Résultat attendu :**
Les artistes dont la colonne `anneeNaiss` ne contient rien (NULL).

**Explication courte :**
On ne peut pas utiliser `= NULL` en SQL. On utilise `IS NULL` pour tester si une valeur est absente.

---

### Requête 10 — Calculer l'âge des artistes (en 2025) et l'afficher à côté du nom

```sql
SELECT nom, prenom, 2025 - anneeNaiss AS age FROM Artiste;
```

**Résultat attendu :**
Une colonne calculée `age` apparaît à côté du nom et du prénom. Les artistes sans année de naissance affichent NULL pour l'âge.

**Explication courte :**
On peut faire un calcul directement dans le SELECT. `AS age` donne un nom à la colonne calculée pour qu'elle soit lisible.

---

### Requête 11 — Afficher les femmes artistes

La table `Artiste` ne contient pas de colonne `sexe` ou `genre`. Il est donc impossible de filtrer les femmes uniquement avec les colonnes disponibles (idActeur, nom, prenom, anneeNaiss).

> **Je ne sais pas.** La table ne contient pas de colonne permettant d'identifier le sexe d'un artiste.

---

## Phase 3 — Requêtes SQL sur la table `Film`

---

### Requête 1 — Afficher uniquement le titre et l'année de chaque film

```sql
SELECT titre, annee FROM Film;
```

**Résultat attendu :**
Une liste avec deux colonnes : le titre et l'année de chaque film.

**Explication courte :**
On sélectionne uniquement les colonnes `titre` et `annee`, comme demandé.

---

### Requête 2 — Afficher les films sortis entre 2000 et 2010 inclus

```sql
SELECT * FROM Film WHERE annee BETWEEN 2000 AND 2010;
```

**Résultat attendu :**
Les films dont l'année est comprise entre 2000 et 2010.

**Explication courte :**
`BETWEEN` inclut les bornes 2000 et 2010.

---

### Requête 3 — Afficher les films dont le titre contient "mort"

```sql
SELECT titre FROM Film WHERE titre LIKE '%mort%';
```

**Résultat attendu :**
Les films dont le titre contient "mort" (ex : La mort aux trousses).

**Explication courte :**
`%mort%` cherche "mort" n'importe où dans le titre.

---

### Requête 4 — Afficher les films sans résumé

```sql
SELECT * FROM Film WHERE resume IS NULL;
```

**Résultat attendu :**
Les films dont le champ `resume` est vide (NULL).

**Explication courte :**
`IS NULL` permet de trouver les lignes où la colonne ne contient aucune valeur.

---

### Requête 5 — Afficher les films avec une photo renseignée

```sql
SELECT * FROM Film WHERE photo IS NOT NULL;
```

**Résultat attendu :**
Les films qui ont une valeur dans la colonne `photo`.

**Explication courte :**
`IS NOT NULL` est l'inverse de `IS NULL` : on garde les lignes qui ont une valeur.

---

### Requête 6 — Afficher les titres et années des films, par année décroissante

```sql
SELECT titre, annee FROM Film ORDER BY annee DESC;
```

**Résultat attendu :**
La liste des films triée du plus récent au plus ancien.

**Explication courte :**
`ORDER BY annee DESC` trie par année en partant du plus grand nombre.

---

### Requête 7 — Films récents du genre 2 (moins de 10 ans, donc après 2015)

```sql
SELECT titre FROM Film WHERE annee > 2015 AND idGenre = 2;
```

**Résultat attendu :**
Les films de genre 2 sortis après 2015. (Selon les données, il est possible qu'aucun résultat n'apparaisse si les films sont tous antérieurs.)

**Explication courte :**
`AND` combine deux conditions : les deux doivent être vraies en même temps.

---

### Requête 8 — Films appartenant aux genres 1 ou 4

```sql
SELECT titre FROM Film WHERE idGenre = 1 OR idGenre = 4;
```

**Résultat attendu :**
Les films dont `idGenre` vaut 1 ou 4.

**Explication courte :**
`OR` signifie qu'au moins l'une des deux conditions doit être vraie.

---

### Requête 9 — Films dont la photo est au format JPG

```sql
SELECT titre, photo FROM Film WHERE photo LIKE '%.jpg';
```

**Résultat attendu :**
Les films dont le nom de fichier de la photo se termine par `.jpg`.

**Explication courte :**
`'%.jpg'` signifie "n'importe quoi suivi de .jpg". Le `%` est au début donc le nom peut être quelconque.

---

### Requête 10 — Films dont le titre commence par "Le" et du genre 1

```sql
SELECT titre FROM Film
WHERE titre LIKE 'Le%' AND idGenre = 1
ORDER BY titre ASC;
```

**Résultat attendu :**
Les films dont le titre commence par "Le" et qui appartiennent au genre 1, triés par titre.

**Explication courte :**
On combine `LIKE` et `AND` pour deux conditions en même temps. `ORDER BY titre ASC` trie alphabétiquement.

---

### Requête 11 — Films anciens avec affiche disponible (avant 1990)

```sql
SELECT titre, photo FROM Film
WHERE photo IS NOT NULL AND annee < 1990
ORDER BY titre;
```

**Résultat attendu :**
Les films sortis avant 1990 qui ont une photo renseignée.

**Explication courte :**
On filtre avec `IS NOT NULL` pour avoir une photo, et `< 1990` pour ne garder que les anciens films.

---

### Requête 12 — Films des années 80–2000 appartenant aux genres 2 ou 3

```sql
SELECT * FROM Film
WHERE annee BETWEEN 1980 AND 2000 AND idGenre IN (2, 3)
ORDER BY idGenre, annee;
```

**Résultat attendu :**
Les films sortis entre 1980 et 2000 et appartenant aux genres 2 ou 3, triés par genre puis par année.

**Explication courte :**
`IN (2, 3)` est une façon courte d'écrire `idGenre = 2 OR idGenre = 3`. `ORDER BY idGenre, annee` trie d'abord par genre, puis par année à l'intérieur de chaque genre.

---

### Requête 13 — Films contenant "man" ou "gun" dans le titre (en une seule requête)

```sql
SELECT titre FROM Film
WHERE titre LIKE '%man%' OR titre LIKE '%gun%';
```

**Résultat attendu :**
Deux films : Rain Man et Top Gun.

**Explication courte :**
On utilise `OR` entre deux conditions `LIKE`. Chaque `LIKE` cherche un mot dans le titre.

---

## Phase Bonus — Requêtes de comptage

### Compter le nombre total de films

```sql
SELECT COUNT(*) FROM Film;
```

**Résultat attendu :**
Un nombre entier correspondant au nombre de lignes dans la table Film.

**Explication courte :**
`COUNT(*)` compte toutes les lignes de la table.

---

### Compter le nombre de films par année

```sql
SELECT annee, COUNT(*) FROM Film GROUP BY annee;
```

**Résultat attendu :**
Une liste d'années avec le nombre de films pour chaque année.

**Explication courte :**
`GROUP BY annee` regroupe les lignes par année. `COUNT(*)` compte combien il y a de films dans chaque groupe.

---

### Sélectionner les années où plus d'un film est sorti

```sql
SELECT annee, COUNT(*) FROM Film GROUP BY annee HAVING COUNT(*) > 1;
```

**Résultat attendu :**
Uniquement les années où au moins 2 films sont sortis.

**Explication courte :**
`HAVING` est comme un `WHERE` mais pour les résultats d'un `GROUP BY`. On filtre les groupes qui ont un comptage supérieur à 1.

---


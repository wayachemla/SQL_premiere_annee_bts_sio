# TP Bibliothèque – MySQL

---

## 0. Connexion à MySQL (Laragon)

```sql
mysql -u root -p
```

> Pas de mot de passe : appuyer directement sur Entrée.  
> Ne jamais oublier le `;` à la fin de chaque instruction SQL.

---

## 1. Création de la base de données

La convention de nommage est `xybibli` où XY représentent les initiales de l'étudiant.

```sql
create database wybibli;
use wybibli;
```

---

## 2. Création des tables

Avant d'importer les données, il faut créer chaque table en respectant les types et tailles de colonnes des fichiers `.sql` fournis.

> Le message `0 rows affected` après un `CREATE TABLE` est normal : la table est créée mais vide, comme un tableau Excel sans données.

---

### 2a. Table `auteurs`

Structure : `auteurs(idauteur, nom, prenom)`

```sql
CREATE TABLE auteurs (
    idauteur int auto_increment,
    nom varchar(50),
    prenom varchar(50),
    PRIMARY KEY (idauteur)
);
```

Import des données :

```sql
source c:/users/waya/desktop/tp_mysql/tp_1/auteurs.sql;
```

Vérification :

```sql
select * from auteurs;
```

> 42 lignes doivent apparaître si l'import est correct.

---

### 2b. Table `livres`

Structure : `livres(idlivre, isbn, titre, nbpages, dateparu, prix, format)`

```sql
CREATE TABLE livres (
    idlivres int auto_increment,
    isbn varchar(50),
    titre varchar(100),
    nbpage int,
    dateparu date,
    prix int,
    format varchar(50),
    PRIMARY KEY (idlivres)
);
```

Import des données :

```sql
source c:/users/waya/desktop/tp_mysql/tp_1/livres.sql;
```

> `titre` doit être `varchar(100)` et non `varchar(50)`. Avec 50, l'import échoue à la ligne 16 : `Data too long for column 'titre'`.  
> Pour corriger : supprimer la table, recréer avec la bonne taille, puis réimporter.

```sql
drop table livres;
-- recréer avec varchar(100) pour titre, puis :
source c:/users/waya/desktop/tp_mysql/tp_1/livres.sql;
```

> `isbn` est un `varchar` et non un `int` car c'est un numéro de série — on ne fait pas de calcul dessus.

---

### 2c. Table `abonnes`

Structure : `abonnes(idemprunteur, nom, prenom, adresse, code, ville, tel, sexe, datenaiss, nbretards)`

```sql
CREATE TABLE abonnes (
    idemprunteur int auto_increment,
    nom varchar(50),
    prenom varchar(50),
    adresse varchar(100),
    code varchar(10),
    ville varchar(50),
    tel varchar(20),
    sexe char(1),
    datenaiss date,
    nbretards int,
    PRIMARY KEY (idemprunteur)
);
```

```sql
source c:/temp/abonnes.sql;
```

---

### 2d. Table `editeurs`

Structure : `editeurs(idediteur, nom, adresse, code, ville, pays, tel, fax)`

```sql
CREATE TABLE editeurs (
    idediteur int auto_increment,
    nom varchar(50),
    adresse varchar(100),
    code varchar(10),
    ville varchar(50),
    pays varchar(50),
    tel varchar(20),
    fax varchar(20),
    PRIMARY KEY (idediteur)
);
```

```sql
source c:/temp/editeurs.sql;
```

---

## 3. Import depuis un fichier SQL complet (TP Agrégats)

Pour ce TP, le fichier `bibliotheque.sql` contient déjà la création des tables et les données. Il suffit de créer la base, de la sélectionner, puis d'importer.

```sql
create database wybibli;
use wybibli;
source c:/temp/bibliotheque.sql;
```

Commandes utiles pour explorer la base :

```sql
show databases;        -- lister toutes les bases
show tables;           -- lister les tables de la base active
select * from auteurs; -- afficher toute la table auteurs
```

> Pour supprimer une base : `drop database nombasedonnee;`

---

## 4. Requêtes SELECT – Agrégats

Structure des tables :

```
livres(idlivre, isbn, titre, nbpages, dateparu, prix, format)
auteurs(idauteur, nom, prenom)
editeurs(idediteur, nom, adresse, code, ville, pays, tel, fax)
abonnes(idemprunteur, nom, prenom, adresse, code, ville, tel, sexe, datenaiss, nbretards)
```

---

### 4a. COUNT – Compter des lignes

**Nombre d'auteurs → résultat : 42**
```sql
select COUNT(*) from auteurs;
```

**Nombre de livres en format "Poche" → résultat : 44**
```sql
select COUNT(*) from livres where format = 'poche';
```

**Nombre de villes distinctes avec des éditeurs → résultat : 3**
```sql
select COUNT(DISTINCT ville) from editeurs;
```

> `DISTINCT` permet d'éliminer les doublons avant de compter.

**Nombre de titres contenant le mot "Fondation" → résultat : 2**
```sql
select COUNT(*) AS nb_titres_livres from livres where titre like 'Fondation%';
```

> `LIKE` avec `%` correspond à "commence par". Pour "contient" : `'%Fondation%'`.

**Nombre d'abonnés → résultat : 97**
```sql
select COUNT(*) from abonnes;
```

**Abonnés sans aucun retard → résultat : 47**
```sql
select COUNT(*) from abonnes where nbretards = 0;
```

**Abonnés à Antibes sans retard → résultat : 7**
```sql
select COUNT(*) from abonnes where ville = 'Antibes' and nbretards = 0;
```

**Abonnés nommés "Sticca" → résultat : 4**
```sql
select COUNT(*) from abonnes where nom = 'Sticca';
```

---

### 4b. AVG – Calculer une moyenne

**Nombre moyen de pages par livre → résultat : 230**
```sql
select AVG(nbpages) AS moyenne_pages_livres from livres;
```

**Nombre moyen de pages en format "Poche" → résultat : 234.3182**
```sql
select AVG(nbpages) AS moyenne_pages_livres from livres where format = 'poche';
```

**Nombre moyen de retards → résultat : 2.1031**
```sql
select AVG(nbretards) from abonnes;
```

---

### 4c. MAX / MIN / SUM – Valeurs extrêmes et totaux

**Livre le plus épais → résultat : 530 pages**
```sql
select MAX(nbpages) from livres;
```

**Total de toutes les pages → résultat : 15870**
```sql
select SUM(nbpages) from livres;
```

**Date de naissance du plus jeune abonné → résultat : 1988-11-24**
```sql
select MAX(datenaiss) from abonnes;
```

> La date la plus récente = le plus jeune. `MAX` sur une date retourne la plus grande valeur (la plus récente).

**Titre du livre le moins épais**
```sql
select titre from livres where nbpages = (select MIN(nbpages) from livres);
```

**Nom de l'abonné le plus jeune → résultat : ROUX**
```sql
select nom from abonnes where datenaiss = (select MAX(datenaiss) from abonnes);
```

---

### 4d. AS – Renommer une colonne

```sql
select COUNT(DISTINCT ville) AS nombre_villes from editeurs;
```

---

### 4e. Requêtes difficiles

**Pourcentage d'abonnés sans retard → résultat : 0.4845 (48,45%)**
```sql
select COUNT(*) / (select COUNT(*) from abonnes) as pct_sans_retard
from abonnes where nbretards = 0;
```

**Âge moyen des abonnés → résultat : 77.59**
```sql
select AVG(datediff(current_date(), datenaiss) / 365.25) AS age_moyen from abonnes;
```

> `datediff(current_date(), datenaiss)` calcule le nombre de jours entre aujourd'hui et la date de naissance.

**Retards moyens par sexe (hommes vs femmes)**
```sql
select sexe, AVG(nbretards) AS moyenne_retards from abonnes GROUP BY sexe;
```

> `GROUP BY` regroupe les résultats par valeur d'une colonne. Ici on obtient une ligne par sexe.

**Nombre d'abonnés par famille (même nom)**
```sql
select nom, COUNT(*) AS nb_membres from abonnes GROUP BY nom ORDER BY nb_membres DESC;
```

---

## 5. Aide-mémoire

### Gestion des bases et tables

```sql
show databases;              -- lister les bases
create database nombase;     -- créer une base
use nombase;                 -- sélectionner une base
drop database nombase;       -- supprimer une base
show tables;                 -- lister les tables
drop table nomtable;         -- supprimer une table
```

### Fonctions d'agrégat

```sql
COUNT(*)            -- compter toutes les lignes
COUNT(DISTINCT col) -- compter les valeurs uniques
AVG(col)            -- moyenne
SUM(col)            -- somme
MAX(col)            -- valeur maximum
MIN(col)            -- valeur minimum
```

### Clauses utiles

```sql
WHERE condition              -- filtre les lignes
LIKE 'mot%'                  -- recherche textuelle (% = joker)
DISTINCT                     -- éliminer les doublons
AS alias                     -- renommer une colonne
GROUP BY colonne             -- regrouper par valeur
ORDER BY colonne DESC        -- trier décroissant
datediff(date1, date2)       -- différence en jours entre deux dates
current_date()               -- date d'aujourd'hui
```

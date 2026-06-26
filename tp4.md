# Compte-rendu de TP — Bibliothèque – Agrégats

## Exercice : Bibliothèque – Agrégats (Exercice 5)

### Tableau d'informations

| Champ | Valeur |
|---|---|
| Étudiant | Non fourni |
| Base de données | xybibli |
| Module | Base de données — BTS SIO 1ère année |

---

## Phase 1 — Création de la base de données

```sql
create database xybibli ;

use xybibli ;
```

---

## Phase 2 — Importation des données

D'après le document « Importation des données de la bibliothèque », on place le fichier dans `c:\temp` puis on l'importe :

```sql
source c:/temp/bibliotheque.sql ;
```

### Vérification

```sql
select * from auteurs;
```

Cette commande permet de vérifier que les données ont bien été importées.

**Structure de la base (tables fournies) :**

- `livres(idlivre, isbn, titre, nbpages, dateparu, prix, format)`
- `auteurs(idauteur, nom, prenom)`
- `editeurs(idediteur, nom, adresse, code, ville, pays, tel, fax)`
- `abonnes(idemprunteur, nom, prenom, adresse, code, ville, tel, sexe, datenaiss, nbretards)`

---

## Phase 3 — Requêtes SQL (SELECT avec agrégats)

### Compter le nombre d'auteurs

```sql
select count(*) as nb_auteurs from auteurs;
```

**Résultat attendu :** 42

**Explication courte :** `count(*)` compte le nombre de lignes dans la table auteurs.

---

### Compter le nombre de livres en format "poche"

```sql
select count(*) as nb_poche from livres where format = 'Poche';
```

**Résultat attendu :** 44

**Explication courte :** On compte les lignes de la table livres en ne gardant que celles où format vaut "Poche".

---

### Compter le nombre de villes où il y a des éditeurs présents

```sql
select count(distinct ville) from editeurs;
```

**Résultat attendu :** 3

**Explication courte :** `distinct` permet de ne pas compter plusieurs fois la même ville.

---

### Nombre moyen de pages par livre

```sql
select avg(nbpages) as moyenne_pages from livres;
```

**Résultat attendu :** 230

**Explication courte :** `avg()` calcule la moyenne de la colonne nbpages.

---

### Nombre moyen de pages par livre en format "poche"

```sql
select avg(nbpages) as moyenne_pages from livres where format = 'Poche';
```

**Résultat attendu :** 234.3182

**Explication courte :** Même principe que la requête précédente, mais avec un filtre sur le format.

---

### Titres de livres contenant le mot "Fondation"

```sql
select count(*) as nb_foundation from livres 
where titre like '%Fondation%';
```

**Résultat attendu :** 2

**Explication courte :** `like '%Fondation%'` cherche le mot "Fondation" n'importe où dans le titre.

---

### Nombre de pages du livre le plus épais

```sql
select max(nbpages) as nbpagesmax from livres;
```

**Résultat attendu :** 530

**Explication courte :** `max()` donne la plus grande valeur de la colonne.

---

### Nombre d'abonnés

```sql
select count(*) as nb_abonnes from abonnes;
```

**Résultat attendu :** 97

**Explication courte :** On compte le nombre de lignes de la table abonnes.

---

### Nombre moyen de retards

```sql
select avg(nbretards) as moy_retards from abonnes;
```

**Résultat attendu :** 2.1031

**Explication courte :** `avg()` sur la colonne nbretards.

---

### Abonnés sans aucun retard

```sql
select count(*) as nb_sans_retard from abonnes 
where nbretards = 0;
```

**Résultat attendu :** 47

**Explication courte :** On filtre les abonnés ayant nbretards égal à 0.

---

### Abonnés à Antibes sans retard

```sql
select count(*) as nb_sans_retard from abonnes 
where nbretards = 0 and ville ='Antibes';
```

**Résultat attendu :** 7

**Explication courte :** Deux conditions combinées avec `and`.

---

### Abonnés nommés "Sticca"

```sql
select * as nb from abonnes where nom='Sticca';
```

**Résultat attendu :** 4

**Explication courte :** On filtre sur le nom de famille pour voir les lignes correspondantes.

---

### Date de naissance du plus jeune abonné

```sql
select max(datenaiss) as date_jeune from abonnes;
```

**Résultat attendu :** '1988-11-24'

**Explication courte :** La date la plus récente correspond à la personne la plus jeune.

---

### Total de pages pour lire tous les livres

```sql
select sum(nbpages) as total_pages from livres;
```

**Résultat attendu :** 15870

**Explication courte :** `sum()` additionne toutes les valeurs de nbpages.

---

## Phase 4 — Requêtes avancées (Difficile)

### Pourcentage d'abonnés sans retard

```sql
select count(*)/ (select count(*) from abonnes) as pourcentage 
from abonnes where nbretards = 0;
```

**Résultat attendu :** 0.4845 soit 48,45%

**Explication courte :** Une requête imbriquée est utilisée : on divise le nombre d'abonnés sans retard par le nombre total d'abonnés.

---

### Comparaison retards femmes / hommes

```sql
select avg(nbretards)/ 
(select avg(nbretards) from abonnes where sexe ='M') 
as pourcentageFH 
from abonnes where sexe='F';
```

**Résultat attendu :** 1.36927945 soit 136,92%

**Explication courte :** On divise la moyenne des retards des femmes par la moyenne des retards des hommes, en une seule requête grâce à une sous-requête.

---

### Âge moyen des abonnés

```sql
select avg(age) from 
(select datediff(current_date(),datenaiss)/365 as age from abonnes) as temp ;
```

**Résultat attendu :** 77.59449230

**Explication courte :** On calcule d'abord l'âge de chaque abonné avec `datediff`, puis on fait la moyenne de cette table temporaire.

---

### Nom de l'abonné le plus jeune

Deux solutions :

```sql
select nom from abonnes where datenaiss = 
   (select max(datenaiss) from abonnes);
```

ou bien

```sql
select nom from abonnes order by datenaiss desc limit 1;
```

**Résultat attendu :** ROUX

**Explication courte :** La première solution compare à la date max, la deuxième trie et prend la première ligne.

---

### Titre du livre le moins épais

```sql
select titre from livres order by nbpages limit 1;
```

**Résultat attendu :** Les Bidochons usent le forfait

**Explication courte :** On trie par nombre de pages croissant et on garde la première ligne.

---

### Question bonus : abonnés par famille

```sql
select nom, count(*) as nb from abonnes group by nom;
```

**Explication courte :** `group by nom` regroupe les lignes ayant le même nom, et `count(*)` compte le nombre de personnes dans chaque groupe.

---

## Phase finale — Vérification

- Les tables `livres`, `auteurs`, `editeurs`, `abonnes` existent bien dans le script `bibliotheque.sql` fourni.
- Les colonnes utilisées (`nom`, `prenom`, `nbpages`, `format`, `titre`, `ville`, `nbretards`, `sexe`, `datenaiss`) existent bien dans la structure fournie.
- Aucune invention de structure.

---

## Question obligatoire finale

**« Ai-je ajouté quelque chose qui n'était pas demandé ? »**

NON.

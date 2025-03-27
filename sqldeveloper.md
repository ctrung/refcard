## SQL

### Commandes pratiques
```sql
alter session set current schema=sys;

select * from v$version;  -- version Oracle
```

### Jointures

#### Joindre sur la première ligne seulement

Oracle n'a pas le `distinct on` de postgres, donc il faut rendre unique de manière manuelle (row_number, max/min, fetch first 1 rows only, etc.)

Ref: https://learnsql.com/blog/sql-join-only-first-row/


### Variables
https://forums.oracle.com/ords/apexds/post/pl-sql-101-substitution-vs-bind-variables-6214 \
https://stackoverflow.com/questions/5653423

TLDR; 2 types de variables :
- substitution `&a`
- bind `:a`

Types des variables de substitution
```sql
define a = 1;    -- NUMBER
define b = '1';  -- VARCHAR2

select * from ma_table where num = &a;
select * from ma_table where str = '&a';
```

Comportement variables bind
```sql
var x number;
exec :x := 10;
select :x from dual;
exec select count(*) into :x from dual;
exec print x;

-- si plusieurs rows : ORA-01422 "exact fetch returns more than requested number of rows"
-- si pas de row     : ORA-01403 "no data found"
```

## Export CSV
Dans une feuille de calcul, `Executer un script (F5)`
```sql
clear screen
set pagesize 110
spool "C:\Users\...\export.csv"
set sqlformat csv
select * from ma_table
```

## Historique des requêtes
- Panneau historique SQL (F8)
- %APPDATA%\SQL Developer\SqlHistory (Windows)

## Navigation

- Recherche rapide d'une table en tapant un motif du nom : menu contextuel sur une Connexion, `Navigateur de schéma`
- Afficher les synonymes parmi les tables : menu contextuel sur les tables, `Appliquer un filtre...`, cocher `Inclure les synonymes`

## Raccourcis clavier
(Feuille de calcul)
- Exécuter une instruction : ctrl-<entrée>
- Exécuter un script : F5
- Formater : ctrl-F7
- Requête précédente/suivante : ctrl-<bas> / ctrl-<haut>

(Résultat d'une requête)
- Tout afficher malgré la pagination : ctrl-<end>

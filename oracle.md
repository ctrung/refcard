## Changer le schéma courant
```sql
alter session set current schema=sys;
```

## Connaître la version d'Oracle
```sql
select * from v$version;  -- version Oracle
```

## Joindre sur la première ligne seulement

Oracle n'a pas le `distinct on` de postgres, donc il faut rendre unique de manière manuelle (row_number, max/min, fetch first 1 rows only, etc.)

Ref: https://learnsql.com/blog/sql-join-only-first-row/

## JSON

Les propriété d'un JSON contenu dans une colonne VARCHAR2 peuvent être requêtée. 

```sql
select * from ma_table, JSON_TABLE ( ma_colonne_contenant_du_json, '$'
  COLUMNS (
    propriete_json_1 NULL ON ERROR,
    propriete_json_2 VARCHAR2 (200)
  )
)
where
  autre_colonne = ...            -- colonne de ma_table
  and propriete_json_1 = ...     -- colonne issue de ma_table.ma_colonne_contenant_du_json.propriete_json_1
```

## Variables
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

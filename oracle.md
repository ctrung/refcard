## Changer le schéma courant
```sql
alter session set current schema=sys;
```

## Connaître la version d'Oracle
```sql
select * from v$version;  -- version Oracle
```

## Jdbc

Formats url Jdbc

|Thin style (SID)|Thin style (Service)|Oracle Net connection descriptor style (SID)|Oracle Net connection descriptor style (Service)|
|---|---|---|---|
|jdbc:oracle:thin:@[HOST][:PORT]:SID|jdbc:oracle:thin:@//[HOST][:PORT]/SERVICE|jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(COMMUNITY=TCP)(PROTOCOL=TCP)(Host=...)(Port=...))(CONNECT_DATA=(SID=...)(SERVER=DEDICATED)))|jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(COMMUNITY=TCP)(PROTOCOL=TCP)(Host=...)(Port=...))(CONNECT_DATA=(SERVICE_NAME=...)(SERVER=DEDICATED)))|

[Doc de référence Oracle : Database URLs and Database Specifiers](https://docs.oracle.com/cd/B19306_01/java.102/b14355/urls.htm#BEIJFHHB)

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

## Privilèges

```sql
-- A faire sur les tables, vues, séquences, procédures stockées, etc.
GRANT SELECT ON ... TO <user>;
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

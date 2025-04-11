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

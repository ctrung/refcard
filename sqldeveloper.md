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

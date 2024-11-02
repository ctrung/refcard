
## Connection
```
mysql -u<username> -p
```


## Account and permissions
```
CREATE USER 'username'@'host' IDENTIFIED BY 'password';

GRANT ALL on database_name.* TO 'username'@'host';
FLUSH PRIVILEGES; # optional, already done by GRANT in theory

SHOW GRANTS FOR 'username'@'host';

REVOKE type_of_permission ON database_name.table_name FROM 'username'@'host';

DROP USER 'username'@'host';
```

### Check TLS connectivity

```sh
mysql> status
...
SSL:			Cipher in use is ECDHE-RSA-AES128-GCM-SHA256
...
```

## Database and tables
```
CREATE DATABASE [IF NOT EXISTS] database_name
[CHARACTER SET charset_name]
[COLLATE collation_name];

SHOW CREATE DATABASE database_name;

USE database_name;


SHOW tables;
SHOW CREATE TABLE table_name;

DESC table_name;

```

## Transactions

https://dev.mysql.com/doc/refman/8.4/en/commit.html

```sql
START TRANSACTION
    [transaction_characteristic [, transaction_characteristic] ...]

transaction_characteristic: {
    WITH CONSISTENT SNAPSHOT
  | READ WRITE
  | READ ONLY
}

BEGIN [WORK]
COMMIT [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
SET autocommit = {0 | 1}
```

## Verrous sur les lignes

Utilisés au sein d'une transaction, les deux types de verrous sur les lignes supportés par MySQL permettent de se prémunir de certains écueils lors de mises à jour concurrentes (`UPDATE` et `DELETE`). 

Voir la page https://dev.mysql.com/doc/refman/8.4/en/innodb-locking-reads.html pour les détails et des exemples d'usage.

### SELECT ... FOR SHARE

D'autres transactions peuvent lire et leurs écritures est mise en attente le temps que le verrou soit relâché et les mises à jour visibles.

| Instruction   | Comportement |
| -----------   | ------------ |
| `SELECT`      | Oui          |
| `SELECT FOR SHARE` | Oui     |
| `SELECT FOR UPDATE` | Bloque |
| `UPDATE`      | Bloque       |
| `DELETE`      | Bloque       |

*Exemples*

Tx1 exécute en premier un `SELECT ... FOR SHARE` au sein d'une transaction, Tx2 exécute après un `UPDATE` et est mis en attente à cause du verrou posé par Tx1. L'`UPDATE` ne rend la main que quand Tx1 sera terminée. Alors, les changements seront rendus visibles pour Tx2 et l'UPDATE aura lieu sur des données à jour.

Si Tx1 et Tx2 exécutent tous deux un SELECT ... FOR SHARE, le premier à faire un UPDATE est mis en attente en attendant que le deuxième verrou soit libéré. Si la deuxième transaction fait aussi un UPDATE, celui ci est refusé avec l'erreur `ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction`, ce qui libère au passage son verrou et débloque l'UPDATE de la première transaction.

### SELECT ... FOR UPDATE

Plus contraignant, mêmes les SELECT ... FOR SHARE sont mis en attente le temps que la transaction se termine.

| Instruction   | Comportement |
| -----------   | ------------ |
| `SELECT`      | Oui          |
| `SELECT FOR SHARE` | Bloque  |
| `SELECT FOR UPDATE` | Bloque |
| `UPDATE`      | Bloque       |
| `DELETE`      | Bloque       |


### NOWAIT et SKIP LOCKED

Ajouté à la fin d'un `SELECT ... FOR SHARE|UPDATE`, `NOWAIT` permet de ne pas attendre en retournant une erreur si un verrou est présent.

Ajouté à la fin d'un `SELECT ... FOR SHARE|UPDATE`, `SKIP LOCKED` permet de filtrer les lignes où un verrou est présent. Attention, un sous-ensemble des lignes est donc potentiellement retourné.

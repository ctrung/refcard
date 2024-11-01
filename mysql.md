
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

## Database
```
CREATE DATABASE [IF NOT EXISTS] database_name
[CHARACTER SET charset_name]
[COLLATE collation_name];

SHOW CREATE DATABASE database_name;

USE database_name;
```

## SELECT ... FOR SHARE

https://dev.mysql.com/doc/refman/8.4/en/innodb-locking-reads.html

Utilisé au sein d'une transaction, permet de se prémunir de certains écueils lors de mises à jour concurrentes. 
Concrètement, d'autres transactions peuvent lire et l'écriture est bloquante le temps que la transaction se termine et que les mises à jour soient raffraichies. 

Exemple :

Tx 1 exécute un SELECT ... FOR SHARE
Tx 2 tente un UPDATE mais est bloqué tant que Tx 1 n'a pas fini
Tx 1 exécute un UPDATE
Tx 1 commit ou rollback
L'UPDATE de Tx 2 passe ensuite

En pratique, cela permet de sérialiser les updates de Tx 1 et Tx 2.

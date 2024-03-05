
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

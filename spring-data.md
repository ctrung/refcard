
# Logging JDBC 

`logging.level.org.springframework.jdbc : debug`


# Logging Hibernate

- `logging.level.org.hibernate.SQL : debug` to show queries
- `logging.level.org.hibernate.orm.jdbc.bind : trace` to show parameter values (Hibernate 6 or after)
- `logging.level.org.hibernate.type.descriptor.sql : trace` to show parameter values (Hibernate 5 or before)

See for others (stats, format sql) : https://www.jtips.info/Hibernate/Log

# Transaction management

## Glossary

**Local transaction** : Specific to a single transactional resource (eg. a JDBC connection). 

**Global transaction** : Managed by the container and span multiple transactional resources. 

**JTA** : Java Transaction API. Implementation of global transaction in Java. Supported by all full-blown JEE-compliant application servers (e.g., JBoss, WebSphere, WebLogic, GlassFish, etc...). For stand-alone applications or web containers (e.g., Tomcat, Jetty, and so on), there also exists open source and commercial solutions that provide support for JTA/XA in those environments (e.g., Atomikos, JOTM, Bitronix, etc...).

**XA Protocol** : Open standard used for distributed transaction processing. Uses 2PC (two-phase commit) to ensure atomicity.


## Spring API (bas niveau)

`PlatformTransactionManager` interface.

```java
package org.springframework.transaction;

public interface PlatformTransactionManager extends TransactionManager {
	TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
	void commit(TransactionStatus status) throws TransactionException;
	void rollback(TransactionStatus status) throws TransactionException;
}
```

`TransactionDefinition` interface : Controls the properties of a transaction (propagation, timeout, readonly, isolation, name).

Isolation levels : 
* `TransactionDefinition.ISOLATION_DEFAULT` : Default isolation of underlying datastore.
* `TransactionDefinition.ISOLATION_READ_UNCOMMITTED` : (lowest) Barely a transaction because can see data modified by other uncommited transactions. 
* `TransactionDefinition.ISOLATION_READ_COMMITTED` : Default level in most database. Prevent seeing data modified by other uncommited transactions but read data can be modified by other transactions.
* `TransactionDefinition.ISOLATION_REPEATABLE_READ` : Stricter than `ISOLATION_READ_COMMITTED`. Ensure that once you select some data, you can select the same set again. However if other transactions insert new data, you can still select the new data.
* `TransactionDefinition.ISOLATION_SERIALIZABLE` : The most expensive and reliable isolation level. All transactions are treated as if they were executed one after another.

Propagation behaviours :
* `TransactionDefinition.PROPAGATION_REQUIRED` : Reuse an existing transaction if present, otherwise start a new one.
* `TransactionDefinition.PROPAGATION_SUPPORTS` : Reuse an existing transaction if present, otherwise do nothing (transactionless).
* `TransactionDefinition.PROPAGATION_MANDATORY` : Reuse an existing transaction if present, otherwise throw an exception.
* `TransactionDefinition.PROPAGATION_REQUIRES_NEW` : Always create a new transaction, if one already exists it is suspended.
* `TransactionDefinition.PROPAGATION_NOT_SUPPORTED` : Does not support execution with an active transaction (transactionless) and suspends any existing transaction.
* `TransactionDefinition.PROPAGATION_NEVER`: Does not support execution with an active transaction (transactionless) and throws an exception if a transaction already exist.
* `TransactionDefinition.PROPAGATION_NESTED` : Runs in a nested transaction (aka subtransaction or savepoint). If there is no active transaction, behaves like `TransactionDefinition.PROPAGATION_REQUIRED`.

`TransactionStatus` interface : Allow to get informations on the transaction and control the transaction execution.

```java
package org.springframework.transaction;

public interface TransactionStatus extends SavepointManager {
  boolean isNewTransaction();
  boolean hasSavepoint();
  void setRollbackOnly();
  boolean isRollbackOnly();
  void flush();                  // flushes the underlying session to a datastore if applicable
                                 // (e.g., when using with Hibernate)
  boolean isCompleted();         // has the transaction ended ? (ie commited or rolled back)
}
```




| Ressource           | Manager                      | API                       |
| --------------------| ---------------------------- | ------------------------- |
| DataSource          | DataSourceTransactionManager | DataSourceUtils           |
| EntityManager       | JpaTransactionManager        | EntityManagerFactoryUtils |
| SessionFactoryUtils | HibernateTransactionManager  | SessionFactory            |

## Déclencher un rollback programmatiquement

```java
try {
  // logique métier...
} catch (BusinessCheckedException ex) {
  // rollback programmatiquement
  TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
}
```

## Greffer un aspect sur une méthode transactionnelle

https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/applying-more-than-just-tx-advice.html

## Propagation

- PROPAGATION_REQUIRED
- PROPAGATION_REQUIRES_NEW
- PROPAGATION_NESTED

https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-propagation.html

## Spring Boot

L'autoconfiguration créé un `PlatformTransactionManager` comme par exemple avec `DataSourceTransactionManagerAutoConfiguration` dans le cas d'un projet Spring JDBC.

## Spring Batch

Le post StackOverflow https://stackoverflow.com/questions/75087558/role-of-platformtransactionmanager-with-spring-batch-5 met en lumière le rôle du transaction manager au niveau du step et du job repositoy.

La classe `org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration` gère l'autoconfiguration des batchs dans Spring Boot.


## UnexpectedRollbackException

En cas de propagation de transaction `PROPAGATION_REQUIRED`, si une méthode intérieure modifie le statut de la transaction à rollback-only, et que la méthode extérieure commite, 
alors une `UnexpectedRollbackException` est rejetée.


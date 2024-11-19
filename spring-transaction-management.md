## API bas niveau pour accéder à la ressource 

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


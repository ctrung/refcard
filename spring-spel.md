```java
@Value("#{monbean.method()}")   // exécute une méthode d'un bean appelé "monbean"

@Value("#{stepExecutionContext['test-key']}")  // lecture d'une valeur dans org.springframework.batch.item.ExecutionContext de spring-batch
```

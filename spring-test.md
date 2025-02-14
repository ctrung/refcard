
# Annotations

## Spring

https://docs.spring.io/spring-framework/reference/testing.html

### [@ActiveProfiles](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/ActiveProfiles.html)

Définit les profils à activer pendant les tests.

### [@ContextConfiguration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/ContextConfiguration.html)

Sert à configurer un contexte Spring pour les tests.

```java
@ContextConfiguration("/test-config.xml") 
class XmlApplicationContextTests {
	// class body...
}

@ContextConfiguration(classes = TestConfig.class) 
class ConfigClassApplicationContextTests {
	// class body...
}
```

Voir paragraphe ci dessous [Unit testing `ConfigurationProperties`](https://github.com/ctrung/refcard/edit/main/spring-test.md#unit-testing-configurationproperties) pour une utilisation de la propriété `initializers` de `@ContextConfiguration`.

### [@DirtiesContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/annotation/DirtiesContext.html)

Indique que le contexte Spring a été corrompu, ce qui provoque son éviction du cache de test et permet d'en recharger un nouveau lors du test suivant. 

### @DynamicPropertySource

Utile pour surcharger dynamiquement des propriétés, eg. issues d'un container dynamique. Voir article https://mkyong.com/spring-boot/spring-boot-dynamicpropertysource-example pour un cas d'usage.

### [@SpringJUnitConfig](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/junit/jupiter/SpringJUnitConfig.html)

Annotation composée qui combine `@ExtendWith(SpringExtension.class)` de JUnit Jupiter et `@ContextConfiguration` du Spring TestContext Framework.

### [@TestPropertySource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/TestPropertySource.html)

Sert à configurer les properties d'un test Spring.

```java
@ContextConfiguration
@TestPropertySource("/test.properties") 
class MyIntegrationTests {
	// class body...
}

@ContextConfiguration
@TestPropertySource(properties = { "timezone = GMT", "port: 4242" }) 
class MyIntegrationTests {
	// class body...
}
```

## Spring Boot

https://docs.spring.io/spring-boot/reference/testing/index.html

### [@AutoConfigureTestDatabase](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html#testing.spring-boot-applications.autoconfigured-jdbc)

Permet de reconfigurer la base de données en test, eg. utiliser l'url de test/application.properties à la place de H2 mem par défaut. 

```java
@JdbcTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
```

### [@JdbcTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/autoconfigure/jdbc/JdbcTest.html)

Slice pour tester Spring JDBC. \
Par défaut, les tests sont transactionnels et effectuent un rollback à la fin. \
Une base de données en mémoire est automatiquement configurée.

### [@MockBean](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/mock/mockito/MockBean.html)

Ajoute un mock au contexte Spring.

### @ServiceConnection

Support Spring Boot de `@DynamicPropertySource` : https://mkyong.com/spring-boot/spring-boot-dynamicpropertysource-example

### [@SpringBootTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/SpringBootTest.html)

Contient `@ExtendWith(SpringExtension.class)` (JUnit 5), démarre tout le contexte Spring. \
Privilégier les slices (`@...Test`) pour avoir un contexte plus partiel.

Supporte la définition de properties
```java
@SpringBootTest(properties = {"org=Spring", "name=Boot"})
```

### [@TestComponent](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/TestComponent.html)

Equivalent à `@Component` pour les tests. Non scanné par `@SpringBootApplication`, il l'est cependant par `@ComponentScan`.

### [@TestConfiguration](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/TestConfiguration.html)

Configuration spécifique pour les tests Spring (cad non scanné par `@SpringBootApplication`). \
Permet d'ajouter ou redéfinir certains beans (`spring.main.allow-bean-definition-overriding` nécessaire).

```java

// Façon 1 : configuration externe

@TestConfiguration
public class MaConfigurationDeTest {
  // beans
}

@SpringBootTest
@Import(MaConfigurationDeTest.class)
publis class MesTests {
  // tests
}

// Façon 2 : configuration en tant que classe static

@SpringBootTest
publis class MesTests {
  // tests

  @TestConfiguration
  static class MaConfigurationDeTest {
    // beans
  }

}
```

### [@WebMvcTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/autoconfigure/web/servlet/WebMvcTest.html)

Slice pour tester Spring MVC à la place de `@SpringBootTest`. Fournit notamment un bean `MockMvc`.

```java
import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

@WebMvcTest(MonController.class)
public class MonControllerTest {

  @Autowired
  private MockMvc mockMvc; // bean fourni par @WebMvcTest

  @Test
  public void testHomePage() throws Exception {
    mockMvc.perform(get("/"))
          .andExpect(status().isOk())
          .andExpect(view().name("home"))
          .andExpect(content().string(containsString("Bienvenue...")));
  }
}
```

## Spring Batch

### [@SpringBatchTest](https://docs.spring.io/spring-batch/docs/5.1.1-SNAPSHOT/org/springframework/batch/test/context/SpringBatchTest.html)

Fournit les beans `JobLauncherTestUtils`, `JobRepositoryTestUtils`, `StepScopeTestExecutionListener` et `JobScopeTestExecutionListener`.

# Classes utilitaires

https://docs.spring.io/spring-framework/reference/testing/unit.html#unit-testing-utilities

https://docs.spring.io/spring-framework/reference/testing/support-jdbc.html#integration-testing-support-jdbc-test-utils

* AopTestUtils
* JdbcTestUtils
* ReflectionTestUtils
* TestSocketUtils

* ModelAndViewAssert
* MockHttpServletRequest
* MockHttpSession

# Database support

Spring framework :

- Transaction Rollback and Commit Behavior : https://docs.spring.io/spring-framework/reference/testing/testcontext-framework/tx.html#testcontext-tx-rollback-and-commit-behavior

- JDBC Testing Support (`org.springframework.test.jdbc.JdbcTestUtils`) : https://docs.spring.io/spring-framework/reference/testing/support-jdbc.html
- Testing Data Access Logic with an Embedded Database : https://docs.spring.io/spring-framework/reference/data-access/jdbc/embedded-database-support.html#jdbc-embedded-database-dao-testing
- Executing SQL scripts (programmatiquement, déclarativemlent) : https://docs.spring.io/spring-framework/reference/testing/testcontext-framework/executing-sql.html
 
Spring Boot :

- Embedded Database Support : https://docs.spring.io/spring-boot/reference/data/sql.html#data.sql.datasource.embedded
- Initialize a Database Using Basic SQL Scripts : https://docs.spring.io/spring-boot/how-to/data-initialization.html#howto.data-initialization.using-basic-sql-scripts

# TestContainers support

https://docs.spring.io/spring-boot/reference/testing/testcontainers.html

https://github.com/testcontainers/testcontainers-java-spring-boot-quickstart

Dependency

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-testcontainers</artifactId>
      <scope>test</scope>
    </dependency>
```

# TestPropertyValues.java

https://docs.spring.io/spring-boot/reference/testing/test-utilities.html#testing.utilities.test-property-values

https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/util/TestPropertyValues.html

Cette classe manipule les propriétés de l'environnement Spring. Voir question Stackoverflow [Appropriate usage of TestPropertyValues in Spring Boot Tests](https://stackoverflow.com/a/54758610) pour 
un exemple d'usage avec `@ContextConfiguration(initializers = ...)`.

# TestRestTemplate

Comme `RestTemplate` mais plus facile pour les assertions sur les erreurs 4XX et 5XXX lors des tests.

https://docs.spring.io/spring-boot/reference/testing/test-utilities.html#testing.utilities.test-rest-template

# Unit testing bean validation

https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html#validation-beanvalidation-spring

```java
@SpringJUnitConfig({LocalValidatorFactoryBean.class})
public class ValidatorTest {
    @Autowired
    private Validator validator;

    @Test
    void checkXXX() {
      ...
    }
}
```

# Unit testing `ConfigurationProperties`

https://docs.spring.io/spring-boot/reference/testing/test-utilities.html#testing.utilities.config-data-application-context-initializer

```java
@SpringJUnitConfig(classes = Config.class, initializers = ConfigDataApplicationContextInitializer.class)
@EnableConfigurationProperties(MyProps.class)
class ServiceTest {
  ...
}

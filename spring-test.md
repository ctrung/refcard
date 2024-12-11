
https://docs.spring.io/spring-framework/reference/testing.html

https://docs.spring.io/spring-boot/reference/testing/index.html

# Annotations

## Spring

[@ContextConfiguration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/ContextConfiguration.html) : Sert à configurer un contexte Spring pour les tests d'intégration.

[@DirtiesContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/annotation/DirtiesContext.html) : Indique que le contexte Spring a été corrompu, ce qui provoque son éviction du cache de test et permettra d'en réinitialiser un nouveau pour le test suivant. 

@DynamicPropertySource : Utile pour surcharger dynamiquement des propriétés, eg. issues d'un container dynamique. Voir article https://mkyong.com/spring-boot/spring-boot-dynamicpropertysource-example pour un cas d'usage.

[@TestPropertySource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/TestPropertySource.html) : Sert à configurer les properties d'un test Spring.

## Spring Batch

[@SpringBatchTest](https://docs.spring.io/spring-batch/docs/5.1.1-SNAPSHOT/org/springframework/batch/test/context/SpringBatchTest.html) : Fournit les beans `JobLauncherTestUtils`, `JobRepositoryTestUtils`, `StepScopeTestExecutionListener` et `JobScopeTestExecutionListener`.

## Spring Boot

[@JdbcTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/autoconfigure/jdbc/JdbcTest.html) :  Slice pour tester Spring JDBC à la place de `@SpringBootTest`. Les tests deviennent transactionnels et effectuent un rollback à la fin. Une base de données en mémoire configurée automatiquement est démarrée.

[@MockBean](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/mock/mockito/MockBean.html) : Ajoute un mock au contexte Spring.

@ServiceConnection : Support Spring Boot de `@DynamicPropertySource`. Voir article https://mkyong.com/spring-boot/spring-boot-dynamicpropertysource-example pour un cas d'usage.

[@SpringBootTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/SpringBootTest.html) : Contient `@ExtendWith(SpringExtension.class)` (JUnit 5), démarre tout le contexte Spring. Voir les slices (`@*Test`) pour démarrer un contexte partiel.

[@TestComponent](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/TestComponent.html) : Equivalent à `@Component` mais pour les tests. Non scanné par `@SpringBootApplication`, il l'est cependant par `@ComponentScan`.

[@TestConfiguration](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/TestConfiguration.html) : Configuration spécifique pour les tests Spring (cad non scanné par `@SpringBootApplication`). Permet d'ajouter ou redéfinir certains beans (`spring.main.allow-bean-definition-overriding` nécessaire).

[@WebMvcTest](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/autoconfigure/web/servlet/WebMvcTest.html) : Slice pour tester Spring MVC à la place de `@SpringBootTest`. Fournit notamment un bean `MockMvc`.

# Spring Boot TestContainers support

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

# Exemples

## @ContextConfiguration

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

## @TestConfiguration

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

## @TestPropertySource

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

## @WebMvcTest

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

## Références

Spring \
https://docs.spring.io/spring-framework/reference/testing.html \
https://docs.spring.io/spring-framework/reference/testing/unit.html#unit-testing-utilities \
https://docs.spring.io/spring-framework/reference/testing/support-jdbc.html#integration-testing-support-jdbc-test-utils

Spring Boot \
https://docs.spring.io/spring-boot/reference/testing/index.html \
https://docs.spring.io/spring-boot/reference/testing/test-utilities.html

## Annotations et classes

|Annotation|Projet|Description|
|---|---|---|
|ActiveProfiles|Spring|Profils à activer pendant les tests|
|AutoConfigureTestDatabase|Spring Boot|Configuration BDD de test|
|ContextConfiguration|Spring|Configuration de test|
|DirtiesContext|Spring|Utile pour recharger le contexte si un test a modifié celui ci|
|DynamicPropertySource|Spring|Utile pour surcharger dynamiquement des propriétés, eg. issues d'un container dynamique. Voir article  pour un cas d'usage|
|JdbcTest|Spring Boot|Test Slice JDBC, BDD en mémoire + transactionnel/rollback par défaut|
|MockitoBean (anc. MockBean)|Spring Boot|Ajoute un bean mocké au contexte de test|
|ServiceConnection|Spring Boot|Plus pratique que DynamicPropertySource, pour les testcontainers|
|SpringBatchTest|Spring Batch|Fournit les beans JobLauncherTestUtils, JobRepositoryTestUtils, StepScopeTestExecutionListener et JobScopeTestExecutionListener|
|SpringBootTest|Spring Boot|Tests Spring Boot|
|SpringJUnitConfig|Spring|`@ExtendWith(SpringExtension.class)` + `@ContextConfiguration`|
|TestComponent|Spring Boot|@Component pour les tests, non scanné par @SpringBootApplication, scanné par @ComponentScan|
|TestConfiguration|Spring Boot|Configuration de test non scannée par @SpringBootApplication, ajoute ou redéfinit des beans (option spring.main.allow-bean-definition-overriding nécessaire)|
|TestPropertySource|Spring|Properties de test|
|WebMvcTest|Spring Boot|Test slice Web MVC|

|Classe|Projet|Description|
|---|---|---|
|AopTestUtils|Spring||
|ApplicationContextRunner|Spring Boot|Utile pour tester les classes d'autoconfiguration|
|ConfigDataApplicationContextInitializer|Spring Boot|Initializer pour supporter @ConfigurationProperties| 
|JdbcTestUtils|||
|ModelAndViewAssert|||
|MockHttpServletRequest|||
|MockHttpSession|||
|OutputCaptureExtension|||
|ReflectionTestUtils|||
|TestPropertyValues|||
|TestRestTemplate|||
|TestSocketUtils|||

## BDD

Spring :
- Transaction Rollback and Commit Behavior : https://docs.spring.io/spring-framework/reference/testing/testcontext-framework/tx.html#testcontext-tx-rollback-and-commit-behavior
- JDBC Testing Support (`org.springframework.test.jdbc.JdbcTestUtils`) : https://docs.spring.io/spring-framework/reference/testing/support-jdbc.html
- Testing Data Access Logic with an Embedded Database : https://docs.spring.io/spring-framework/reference/data-access/jdbc/embedded-database-support.html#jdbc-embedded-database-dao-testing
- Executing SQL scripts (programmatiquement, déclaratively) : https://docs.spring.io/spring-framework/reference/testing/testcontext-framework/executing-sql.html
 
Spring Boot :
- Embedded Database Support : https://docs.spring.io/spring-boot/reference/data/sql.html#data.sql.datasource.embedded
- Initialize a Database Using Basic SQL Scripts : https://docs.spring.io/spring-boot/how-to/data-initialization.html#howto.data-initialization.using-basic-sql-scripts
- `DataSourceProperties#determineUsername` sets a `sa` user by default if the database is "embedded" (see EmbeddedDatabaseConnection#isEmbeddedUrl(String url) for the definition)


## TestContainers

https://docs.spring.io/spring-boot/reference/testing/testcontainers.html \
https://github.com/testcontainers/testcontainers-java-spring-boot-quickstart

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-testcontainers</artifactId>
      <scope>test</scope>
    </dependency>
```

## Exemples

ApplicationContextRunner \
https://docs.spring.io/spring-boot/reference/features/developing-auto-configuration.html#features.developing-auto-configuration.testing \
https://stackoverflow.com/questions/59190919/how-to-test-configurationproperties-with-applicationcontextrunner-from-spring-b


AutoConfigureTestDatabase
```java
@JdbcTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
```

ConfigDataApplicationContextInitializer \
https://docs.spring.io/spring-boot/reference/testing/test-utilities.html#testing.utilities.config-data-application-context-initializer
```
@SpringJUnitConfig(classes = Config.class, initializers = ConfigDataApplicationContextInitializer.class)
@EnableConfigurationProperties(MyProps.class)
class ServiceTest {
  ...
}
```

ContextConfiguration
```java
@ContextConfiguration("/test-config.xml") 
class XmlApplicationContextTests {
	...
}

@ContextConfiguration(classes = TestConfig.class) 
class ConfigClassApplicationContextTests {
	...
}
```


DynamicPropertySource, ServiceConnection :\
https://mkyong.com/spring-boot/spring-boot-dynamicpropertysource-example


OutputCaptureExtension
```java
@ExtendWith(OutputCaptureExtension.class)

@Autowired
CapturedOutput captured;
assertThat(captured.getOut().lines().toList().getLast()).isEqualTo("ABC");
```


SpringBootTest
```java
@SpringBootTest(properties = {"org=Spring", "name=Boot"})
```

SpringJUnitConfig
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

TestConfiguration
```java

// Façon 1 : configuration externe
@TestConfiguration
public class MaConfigurationDeTest {
  ...
}
@SpringBootTest
@Import(MaConfigurationDeTest.class)
publis class MesTests {
  ...
}

// Façon 2 : configuration en tant que classe static
@SpringBootTest
publis class MesTests {
  @TestConfiguration
  static class MaConfigurationDeTest {
    ...
  }
}
```

TestPropertySource
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

TestPropertyValues \
Explications de Phil Webb et exemples d'usage dans les tests Spring Boot : [Appropriate usage of TestPropertyValues in Spring Boot Tests](https://stackoverflow.com/a/54758610) 



WebMvcTest
```java
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



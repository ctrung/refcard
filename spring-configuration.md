# Docs de référence

External Configuration : https://docs.spring.io/spring-boot/reference/features/external-config.html

External Application Properties : https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files

# Configuration multiples

Par défaut, le fichier de config Spring Boot est `application.[properties|yaml]`.

Il est possible d'en définir un dans un jar, et un autre à l'extérieur pour complémenter/surcharger les propriétés par environnement par exemple. Cependant, il n'est pas possible d'en définir plusieurs au sein de plusieurs jars. A la place, Spring Boot permet :
- Le mélange de fichiers properties et yaml car l'unicité du nom tient compte de l'extension. Cette solution n'est pas recommandée et ne permet finalement d'avoir qu'un seul fichier de config supplémentaire.
- Mise en place d'un sous dossier `config/`, `config/application.[properties|yaml]` complémentant/surchargeant `application.[properties|yaml]` 
- Import d'un ou plusieurs fichiers avec `spring.config.import`. C'est la méthode recommandée depuis Spring Boot 2.4.
- Redéfinition de l'emplacement des fichiers config avec `spring.config.location` ou encore `spring.config.additional-location`. En pratique, l'option est définie en ligne de commandes (ex : `java -jar myproject.jar --spring.config.location=optional:classpath:/default.properties,optional:classpath:/override.properties`) pour être interprétée avant la lecture des properties.

Cas particulier des tests : 

`/src/test/resources/application.yaml` est pris en compte à la place de `/src/java/resources/application.yaml` si présent. Pour surcharger/complémenter, on peut toujours recourir à un `/src/test/resources/config/application.yaml` comme décrit ci-dessus.

# Configurations imbriquées

https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files.importing

Propriété `spring.config.import` avec les fonctionnalités suivantes supportées :
- Optionnel : `spring.config.import=optional:file:./dev.properties`
- Plusieurs (le dernier gagne) : `spring.config.import=optional:file:./dev1.properties,optional:file:./dev2.properties`
- Hint fichier sans extension (utile cloud) : `spring.config.import=file:/etc/config/myconfig[.yaml]`
- Configuration tree (utile cloud) : `spring.config.import=optional:configtree:/etc/config/`

# @ConfigurationProperties (Spring Boot)

```java 
// Méthode 1 : avec @Bean
@Bean
@ConfigurationProperties("spring.batch.datasource")
public DataSourceProperties batchDataSourceProperties() {
    return new DataSourceProperties();
}

// Méthode 2 : avec @EnableConfigurationProperties
@EnableConfigurationProperties(DataSourceProperties)
```

Pour supporter les `ConfigurationProperties` dans un test unitaire Spring : `@SpringJUnitConfig(initializers = ConfigDataApplicationContextInitializer.class)`. \
See [org.springframework.boot.test.context.ConfigDataApplicationContextInitializer](https://docs.spring.io/spring-boot/api/java/org/springframework/boot/test/context/ConfigDataApplicationContextInitializer.html).

Pour supporter de nouveaux types dans les `ConfigurationProperties`, se référer à la doc https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.typesafe-configuration-properties.conversion. Un exemple d'implémentation dans spring-batch 5.2 : `org.springframework.batch.core.converter.DefaultJobParametersConverter`.

# InitializingBean

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/InitializingBean.html

Vu sur https://youtu.be/SwKrrLCVTH0?t=782, InitializingBean en valeur de retour de @Bean :

```java
@Bean
InitializingBean populateTestData(DodgeUserRepository userRepository) {
  return () -> {
    userRepository.save(...);
    userRepository.save(...);
    userRepository.findAll().forEach(System.err::println);
  }
}
```


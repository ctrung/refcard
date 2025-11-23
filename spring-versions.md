# Spring

## Spring 7

[Spring 7 - Release notes](https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-7.0-Release-Notes)

[Blog - Road to GA (série d'articles sur les nouveautés)](https://spring.io/blog/2025/09/02/road_to_ga_introduction) 

### @Retryable

spring-retry est en fin de vie et certaines fonctionnalités sont incluse dans Spring core 7.

Sur la méthode
```java
@Retryable(delayString = "300ms", multiplier = 1.5, jitterString = "200ms")
PlayerStats getPlayerStats(String playerId) {
  ...
}
```

Activation dans la config
```
@EnableResilientMethods
public class Application {
  ...
} 
```

### Versioning API REST

Spring 7+ supporte le versioning des APIs REST :

Niveau @Controller
```java
@RequestMapping(path="/api", produces=MediaType.APPLICATION_JSON_VALUE, version="1.0+")
public class ApiController {
  @GetMapping(path="/queue/stats", version="1.1")
  public QueueStats queueStats() {
    ...
  }
```

Niveau config
```java
@Configuration(proxyBeanMethods = false)
public class WebConfiguration implements WebMvcConfigurer {
  @Override
  public void configureApiVersioning(ApiVersionConfigurer configurer) {
    // alternative 1 : par défaut
    WebMvcConfigurer.super.configureApiVersioning(configurer);

    // alternative 2 : manuel
    configurer.useRequestHeader("API-Version")
            .addSupportedVersions("1.0", "1.1")
            .setDefaultVersion(1.1");
  }
} 
```

Source : 
[Spring 7 Road to GA - API Versioning in Spring](https://spring.io/blog/2025/09/16/api-versioning-in-spring) \
[Paris JUG - Spring Boot: Chapitre 4](https://youtu.be/687mwbaZo8g?t=2634)


## [Spring Framework 6.2: Core Container Revisited by Juergen Hoeller @ Spring I/O 2024](https://www.youtube.com/watch?v=GzX3C0sTFbw)

Slides : https://2024.springio.net/slides/spring-framework-62-core-container-revisited-springio24.pdf

- Fast path for bean name matching (before was occuring after by type matching as a last resort).
  **NB** : needs `-parameters` compile option to preserve parameters name.

Reminder on bean configuration ways (not new) :
```java
// by bean name
@Bean
public DataSource commonDataSource() {
 ...
}
@Bean
public MyRepository repository(DataSource commonDataSource) {
 ...
}

// by qualifier
@Bean @MyQualifier
public DataSource commonDataSource() {
 ...
}
@Bean
public MyRepository repository(@MyQualifier DataSource ds) {
 ...
}

// by bean name + qualifier
@Bean
public DataSource commonDataSource() {
 ...
}
@Bean
public MyRepository repository(
 @Qualifier("commonDataSource") DataSource dataSource) {
 ...
}
```

- @Fallback (inverse of @Primary) : mostly useful in configurations where tierce modules beans are not under our responsabilities (ie. theirs as regular beans + ours as @Fallback beans).  
```java
@Bean
public DataSource commonDataSource() { // only non-fallback
 ...
}
@Bean @Fallback
public DataSource otherDataSource() {
 ...
}
```

- Singleton initialization lock mecanism rewritten (from synchronized to reentrant lock based).



Lifecycle management
```java
@Bean
public ThreadPoolTaskExecutor taskExecutor() {
 …
 executor.setPhase(...);
 …
}

@Bean
public ThreadPoolTaskScheduler taskScheduler() {
 …
 scheduler.setPhase(...);
 …
}
```

# Spring Boot
## Liens 

https://spring.io \
https://spring.io/blog \
https://start.spring.io

[Chaîne YT SpringDeveloper](https://www.youtube.com/user/SpringSourceDev) \
[Chaîne YT Spring I/O](https://www.youtube.com/c/SpringIOConference)

## Releases

![image](https://github.com/user-attachments/assets/517549e6-04c4-427b-bcda-8a46af2d830a)

![image](https://github.com/user-attachments/assets/c48da473-02ee-4574-91be-bb4a1baa66e1)


2.0 : [Prez par Phil Webb et Madhura Bhave](https://www.youtube.com/watch?v=TasMZsZxLCA) \
2.3 : [Prez par Phil Webb](https://www.youtube.com/watch?v=WL7U-yGfUXA) \
2.4 : [Prez par Phil Webb](https://www.youtube.com/watch?v=lgyO9C9zdrg) \
2.6 : [Prez par Dan Vega](https://www.youtube.com/watch?v=4L4LEnawcO8) \
3.0-SNAPSHOT (26 oct 2022) : [Mini préz avant 1ère par Josh Long](https://www.youtube.com/watch?v=aUm5WZjh8RA) \
3.2 : [Tips by Josh Long](https://www.youtube.com/watch?v=dMhpDdR6nHw)

Retrospective (2025-01-30) : [Spring Boot co-creator Phil Webb and its biggest fan Josh Long look at its first ten whole years!](https://www.youtube.com/watch?v=SwKrrLCVTH0)

## Spring boot 4

[Demo app Spring Boot 4 par Brian Clozel et ](https://github.com/bclozel/matchmaking)

## Tips

### Actuator

```
management.endpoints.web.exposure.include=*
management.endpoints.web.exposure.exclude=*
```

URLs de quelques endpoints : 
- `/actuator`
- `/actuator/configprops`
- `/actuator/env/<prop>` 

### Arrêt graceful du serveur web

Depuis 2.3, nouvelle propriété `server.shutdown=immediate` (par défaut) \
ou plus user-friendly `server.shutdown=graceful` en combinaison avec `spring.lifecycle.timeout-per-shutdown-phase=20s`

### ConfigurationProperties POJO

Prendre exemple sur `ServerProperties` au sein de Spring Boot.

```java
import org.springframework.boot.convert.DurationUnit;
import org.springframework.boot.context.properties.bind.Name;

@ConfigurationProperties("my.service")

@DefaultValue("USER") List<String> roles
@DefaultValue Security security                  // utile pour avoir une instance au lieu de null

@ConstructorBinding                              // utile si plusieurs constructeurs

@DurationUnit(ChronoUnit.SECONDS) Duration forTime   // unité par défaut si non spécifié dans la config

@Name("for") Duration forUnit                    // utile pour nommer une propriété sans contrainte de mot clé java
```

### Démarrage

`SpringApplication.run(DemoApplication.class, args)` est un raccourci de `new SpringApplication(DemoApplication.class).run(args)`.

La version "non raccourcie" permet de configurer en détail l'objet SpringApplication, par exemple attacher un `ApplicationStartup` (**2.4**).

```java
SpringApplication springApplication = new SpringApplication(DemoApplication.class);
springApplication.setApplicationStartup(new FlightRecorderApplicationStartup());
springApplication.run(args);
```

```sh
java -XX:StartFlightRecording:filename=recording.jfr,duration=10s -jar app.jar
```

Ou encore d'attacher des properties programatiquement : 

```java
SpringApplication springApplication = new SpringApplication(DemoApplication.class);
Properties properties = new Properties();
properties.put("spring.batch.job.enabled", false);
application.setDefaultProperties(properties);
```

### Probe support

Depuis la 2.3, https://www.youtube.com/watch?v=WL7U-yGfUXA&t=1566s.

Endpoint actuator : 
- `/actuator/health`
- `/actuator/health/liveliness`
- `/actuator/health/readiness`

Il est possible de changer l'état
```java
@Autowired
private ApplicationEventPublisher publisher;

AvailabilityChangeEvent.publish(publisher, this, ReadinessState.REFUSING_TRAFFIC);
AvailabilityChangeEvent.publish(publisher, this, ReadinessState.ACCEPTING_TRAFFIC);
```


### Properties

#### Imports

Depuis la 2.4, `spring.config.import` pour splitter la configuration qui est plus souple que les profils à cet effet. 

L'extension est aussi optionnelle grâce à la syntaxe `[.extension]`. Dans l'exemple, les deux fichiers ext et ext.properties sont acceptés.

Aussi, les propriétés peuvent provenir d'une arborescence à la manière d'Ansible ou de Kubernetes (niveau intermédiaire = dossier, feuille = fichier). L'arborescence doit exister par défaut, mais le mot clef `optional` permet de passer outre l'erreur.

A la manière de configtree, il est possible d'implémenter son propre type d'import. S'inspirer des classes `ConfigTreeConfigDataLocationResolver implements ConfigDataLocationResolver<ConfigTreeConfigDataResource>` et `ConfigTreeConfigDataLoader implements ConfigDataLoader<ConfigTreeConfigDataResource>`. Vu dans https://www.youtube.com/watch?v=lgyO9C9zdrg&t=2451s.

```properties
spring.config.import=classpath:example1.properties,classpath:example2.properties
spring.config.import=file:${user.home}/demo/ext[.properties]
spring.config.import=optional:configtree:${user.home}/demos/tree/
```

Actuator insights : 
- Pour les imports complexes (eg. chainés), `/actuator/configprops` affiche les propriétés et le fichier properties d'origine où elles sont définies. 
- `/actuator/env/<prop>` donne le détail pour une propriété.

#### Multi-docs

```yaml
spring:
  application:
    name: "test"
---
spring:
  profiles: "prod"
  application:
    name: "test-prod"
```

Depuis la 2.4, construction multi docs supportée dans les fichiers properties. \
La chaîne de caractère `#---` est l'équivalent de `---` en yaml. \
Si plusieurs profils correspondent, la règle et que le dernier l'emporte, cad wait.for=5s dans l'exemple ci dessous. 
Au passage, on remarque que l'on peut mettre une SpEL dans la propriété `spring.profiles`.

```properties
wait.for=1s
spring.profiles.active=test

#---

spring.profiles=prod | test
wait.for=10s

#---

spring.profiles=test
wait.for=5s
```

#### Profiles

Depuis la 2.4, la propriété `spring.profiles` est dépréciée au profit de `spring.config.activate.on-profile`, la raison étant que l'ancienne clé était un préfixe d'autres propriétés comme `spring.profiles.active`, ce qui est plus difficile à écrire en yaml par exemple.

Plusieurs variantes : 
- `spring.config.activate.on-profile=prod | test`
- `spring.config.activate.on-cloud-platform=kubernetes`  (en combinaison avec `spring.main.cloud-platform`)

# Spring Batch

## Version 6

[Coffe + Software with Spring Batch lead Mahmoud Ben Hassine - Sept 19th 2025](https://www.youtube.com/live/JOiGP7y60eA?si=nBbf2JUBAgHpY1nI)

- retry lib moving to Spring core : legacy retry from Spring Batch still present for back compatibility and will be maintained this generation before removal  
- by default state management will be opt in
- api is nullability compliant with jspecify
- redis repository coming in 6.1
- new concurrency model to minimize locking => performance gain
- metrics support with JFR


## Liens 

https://spring.io \
https://spring.io/blog \
https://start.spring.io

[Chaîne YT SpringDeveloper](https://www.youtube.com/user/SpringSourceDev) \
[Chaîne YT Spring I/O](https://www.youtube.com/c/SpringIOConference)

## Présentation nouvelles releases

2.0 : [Prez par Phil Webb et Madhura Bhave](https://www.youtube.com/watch?v=TasMZsZxLCA) \
2.3 : [Prez par Phil Webb](https://www.youtube.com/watch?v=WL7U-yGfUXA) \
2.4 : [Prez par Phil Webb](https://www.youtube.com/watch?v=lgyO9C9zdrg) \
2.6 : [Prez par Dan Vega](https://www.youtube.com/watch?v=4L4LEnawcO8) \
3.0-SNAPSHOT (26 oct 2022) : [Mini préz avant 1ère par Josh Long](https://www.youtube.com/watch?v=aUm5WZjh8RA) \
3.2 : [Tips by Josh Long](https://www.youtube.com/watch?v=dMhpDdR6nHw)

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

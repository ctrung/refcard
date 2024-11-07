Librairie pour intégrer les composants OSS de Netflix, principalement **Eureka**, le serveur de découverte des services.

https://github.com/spring-cloud/spring-cloud-netflix

## Config

Source un peu vieille (Definitive Guide to Spring Batch sorti en 2019), à revoir potentiellement.

*Côté serveur Eureka*

Démarrage en local : `spring cloud eureka` ou `spring cloud configserver eureka`

URL par défaut dashboard Eureka : http://localhost:8761

*Côté service qui veut s'enregistrer*

```xml
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

```java
@EnableDiscoveryClient
@SpringBootApplication
public class RestServiceApplication {
  ...
}
```

```yml
# à mettre dans le bootstrap.yml au lieu du application.yml
spring:
  application:
    name: <nom du service à enregistrer>
```

*Côté client qui veut utiliser un service*

```xml
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

```java
@EnableDiscoveryClient(autoRegister = false)
@SpringBootApplication
public class RestServiceApplication {
  ...
}

@Bean
@LoadBalanced
public RestTemplate restTemplate() {
        return new RestTemplate();
}

// Exemple d'appel RestTemplate
ResponseEntity<String> responseEntity = this.restTemplate.exchange(
                                            "http://<nom du service tel que enregistré dans Eureka>/<endpoint REST>",
                                            HttpMethod.GET,
                                            null,
                                            String.class);
```

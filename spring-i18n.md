# References

Spring Framework - [MessageSource](https://docs.spring.io/spring-framework/docs/6.2.x/javadoc-api/org/springframework/context/MessageSource.html) (javadoc)

Spring Boot - [Internationalization](https://docs.spring.io/spring-boot/reference/features/internationalization.html)

# Démarrage (Spring Boot)

L'auto-configuration créé un `MessageResource` par défaut si le resourceBundle `messages` est présent.

Spécifique à une locale `messages_xx_XX.properties` ou par défaut `messages.properties`
```properties
mon.message=bonjour
```

```java
@Autowired
MessageSource messageSource;

messageSource.getMessage("mon.message", null, "default", Locale.getDefault()));   // bonjour
```

# Personnaliser le MessageResource

```java
@Bean
public MessageSource messageSource() {
  ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
  messageSource.setBasenames("messages");
  messageSource.setDefaultEncoding("ISO-8859-1");
  return messageSource;
}
```

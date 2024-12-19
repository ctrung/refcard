# Reference

Bean Validation - [Homepage](https://beanvalidation.org/)

Spring Framework - [Java Bean Validation](https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html)


Spring Boot - [Validation](https://docs.spring.io/spring-boot/reference/io/validation.html)

# Démarrage (Spring Boot)

Ajouter le starter pour récupérer jakarta-validation et hibernate-validator :
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

# Method Validation (Jakarta Bean Validation)

Valider les paramètres et valeur de retour d'une méthode. A noter la présence de `@Validated` sur la classe.

```java
@Service
@Validated
public class ValidatedService {

    public String findByCodeAndAuthor(@Size(min = 8, max = 10) String code, String author) {
        ...
    }
}

validatedService.findByCodeAndAuthor("12345678", "Clément");    // OK
validatedService.findByCodeAndAuthor("123456",   "Clément");    // KO, jakarta.validation.ConstraintViolationException: findByCodeAndAuthor.code: la taille doit être comprise entre 8 et 10
```

# Validation manuelle

```java
class Author {
  @NotNull
  String name;
  @NotNull
  String book;

  public Author(String name, String book) {
    this.name = name;
    this.book = book;
  }
}

@Autowired
Validator validator;

Set<ConstraintViolation<Author>> violations = validator.validate(new Author(null, "XYZ"));
log.info("violations : {}", violations);                                        // violations : [ConstraintViolationImpl{interpolatedMessage='ne doit pas être nul', propertyPath=name, rootBeanClass=class com.example.spring.batch.SpringBatchApplication$Author, messageTemplate='{jakarta.validation.constraints.NotNull.message}'}]
log.info("violation.message : {}", violations.iterator().next().getMessage());  // violation.message : ne doit pas être nul
```

# Messages et i18n

Le basename du resourceBundle est `ValidationMessages` par défaut.

Certains messages ne sont pas dynamiques, eg. `@NotNull`. Une alternative est de personnaliser le message tout en gardant la traduction par défaut :

```java
@NotNull(message = "L'auteur {jakarta.validation.constraints.NotNull.message}")
String name;
```

Jakarta Bean Validation - [Default message interpolation algorithm](https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#validationapi-message-defaultmessageinterpolation-resolutionalgorithm) \
Jakarta Bean Validation - [Appendix B: Standard ResourceBundle messages](https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#standard-resolver-messages) \
Spring Framework - [LocalValidatorFactoryBean.setValidationMessageSource(MessageSource)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html#setValidationMessageSource(org.springframework.context.MessageSource)) (javadoc)



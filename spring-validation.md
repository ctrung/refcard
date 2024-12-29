# Reference

Bean Validation - [Homepage](https://beanvalidation.org/)

Hibernate validator - [Doc](https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single)

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

Pour changer le resourceBundle par défaut, eg. par `messages.properties` qui est le choix par défaut dans Spring Boot : 

```java
@Bean
public Validator getValidator(MessageSource messageSource) {
  LocalValidatorFactoryBean factory = new LocalValidatorFactoryBean();
  factory.setValidationMessageSource(messageSource);
  return factory;
}
```

Jakarta Bean Validation - [Default message interpolation algorithm](https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#validationapi-message-defaultmessageinterpolation-resolutionalgorithm) \
Jakarta Bean Validation - [Appendix B: Standard ResourceBundle messages](https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#standard-resolver-messages) \
Spring Framework - [LocalValidatorFactoryBean.setValidationMessageSource(MessageSource)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html#setValidationMessageSource(org.springframework.context.MessageSource)) (javadoc)

# Créer sa propre Constraint

Avant de créer sa propre `Constraint`, vérifier qu'elle n'existe pas déjà dans :

- Hibernate validator additional constraints : https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#validator-defineconstraints-hv-constraints
- https://github.com/nomemory/java-bean-validation-extension
- ou via une composition : https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#constraintsdefinitionimplementation-constraintcomposition

Exemple sur un bean :

```java

@Documented
@Constraint(validatedBy = AllNotNullValidator.class)
@Target({ TYPE })
@Retention(RUNTIME)
@interface AllNotNull {
  String message() default "{com.acme.constraint.MyConstraint.message}";
  Class<?>[] groups() default {};
  Class<? extends Payload>[] payload() default {};
}

class AllNotNullValidator implements ConstraintValidator<AllNotNull, Author> {
  @Override
  public boolean isValid(Author value, ConstraintValidatorContext context) {
    return value.name != null && value.favoriteManga != null;
  }
}

@AllNotNull
record Author (String name, String favoriteManga) {
}
```

Lecture utile : 

https://jakarta.ee/specifications/bean-validation/3.0/jakarta-bean-validation-spec-3.0.html#constraintsdefinitionimplementation-constraintdefinition-properties


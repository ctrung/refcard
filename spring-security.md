
[Spring Tips: Spring Security method security with special guest Rob Winch](https://youtu.be/JYZHp5eqS2I?si=_Y3BuRshWnTJlUCL)

## Authorization

### For domain objects (since spring security 6.3)

See https://docs.spring.io/spring-security/reference/whats-new.html#_secure_return_values_14596_14597

```java
// domain object
public class DomainObject {
  @PreAuthorize("this.owner == authentication?.name")
  public String getFieldXXX() {
    ...
  }
}

// method callpoint
public interface DomainObjectService {
  @AuthorizeReturnObject
  DomainObject findById(int id);
} 

```

### For controllers, services and repositories

```java
public class Service {

  @PostAuthorize("returnObject.owner == authentication?.name")
  MyObject findById(int id) {
      ...
  }
}
```

### Proxies activation

(manual way)
```java
AuthorizationProxyFactory proxyFactory = AuthorizationAdvisorProxyFactory.withDefaults();
ServiceApi service = (ServiceApi) roxyFactory.proxy(new ServiceImpl());
```

(automatic way)
```java
@SpringBootAplication
@EnableMethodSecurity            // <-------
public class MyAPplication {
  public static void main(String args) {
    ...
  }
}
```

### Handlers

Example of a masked data if insufficient access 

```java
// entity
public class Entity {
  @PreAuthorize("this.owner == authentication?.name")
  @HandleAuthorizationDenied(handlerClass = MaskMethodAuthorizationDeniedHandler.class)
  public String getFieldXXX() {
    ...
  }
}

@Component
public class MaskMethodAuthorizationDeniedHandler implements MethodAuthorizationDeniedHandler {
  public Object handleDeniedInvocation(MethodInvocation mi, AuthorizationResult ar) {
    return "****";
  }
}
```


## Unit testing

(manual way)
```java
@Test
void testXXX() {
  SecurityContextHolder.getContext().setAuthentication(new TestingTokenAuthentication("john", "credentials", "ROLE_USER"));
  ...
}
```

(automatic way)
```java
@WithMockUser("john")
@Test
void testXXX() {
  ...
}
```


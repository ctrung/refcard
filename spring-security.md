## Authorization

(manual way)
```java
public class Service {

  MyObject findById(int id) {
    Principal principal = SecurityContextHolder.getContext().getAuthentication();

    MyObject myObj = ...

    if(!myObj.getOwner().equals(principal.getName())) {
      ...
    throw new AuthorizationDeniedException("Dneied", new AuthorizationDecision(false));
    }
  }
}

(automatic way)
```java
public class Service {

  @PostAuthorize("returnObject.owner == authentication?.name")
  MyObject findById(int id) {
      ...
  }
}
```
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


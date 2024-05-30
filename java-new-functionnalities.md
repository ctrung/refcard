## Java 22

### JEP-447 : Déclarations avant super()

```java
public class A extends B {

  public A(int value) {
    // Nouveau : on peut ajouter du code avant l'appel à super() s'il ne modifie pas l'état de l'instance
    if(value > 0) {
      throw new IllegalArgumentException(); 
    }
    super(); 
  }
}
```

### JEP 456 : Variables et modèles sans noms

Remplacé par un underscore si on ne compte pas utiliser la variable.

```java
for (Element _: elements) {
   // Bloc qui n'utilise pas les éléments 
}

var _ = mySet.remove(myObject);

try {

} catch(NumberFormatException _) {
   logger.error("Pas un nombre");
}
```



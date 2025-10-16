## Introduction

Par défaut, les objets sont "nullable" en java. Non contrôlé, un objet nullable provoque potentiellement un Null Pointer Exception (NPE) au runtime. 

Il existe des solutions pour éviter les NPE :
- L'API `Optional` mais celle ci est invasive et non généralisable à toutes les situations (concue pour les valeurs de retour comme dans l'API Stream)
- Annotations reconnues par les IDE comme JSR-305, Jetbrains, Spring, etc...

**JSpecify** est une nouvelle librairie d'annotations de détection des NPE lors du dev qui complétée de **NullAway** (extension **ErrorProne**) bloque celles ci lors du build. \
Contrairement aux autres librairies d'annotations, elle est standardisée et adoptée par la communauté java. Elle est plus puissante puisqu'on peut aussi annoter les objets d'un tableau ou un type paramétré d'un generic.  

## Fonctionnement

Ajouter la dépendance `org.jspecify:jspecify:1.0.0` pour bénéficier des annotations :

|Annotation|Description|
|----------|-----------|
|`@Nullable`|Indique que l'objet peut être `null`|
|`@NonNull`|Indique que l'objet est jamais `null`|
|`@NullMarked`|Se positionne dans un package pour indiquer que les objets sont `@NonNull` par défaut et dans le cas contraire sont explicitement annotés avec `@Nullable`. C'est l'inverse du comportement par défaut en java|
|`@NullUnmarked`|Equivalent au comportement classique en java| 

Créer un `package-info.java` avec `@NullMarked` pour être `@NonNull` par défaut :
```java
@NullMarked
package org.example

import org.jspecify.annotations.NullMarked;
```

JSpecify supporte aussi les classes du JDK et est en cours d'adoption dans Spring 7.

JSpecify n'est utile qu'en dev avec un IDE. Il permet de détecter/bloquer lors du build s'il est complété d'un plugin **ErrorProne** comme **NullAway**.

Les dépendances à ajouter au build sont : `com.google.errorprone:error_prone_core:2.37.0`, `com.uber.nullaway:nullaway:0.12.6`.

Configurer errorprone :
- disableAllChecks (pour ne garder que nullaway)
- option `NullAway:OnlyNullMarked` à `true`
- error `NullAway` pour bloquer au lieu d'afficher des warnings
- option `NullAwayJSpecifyMode` à `true`

## Tips

Si une API retourne une valeur `@Nullable` mais que le cas d'utilisation ne renvoit jamais `null`, on peut utiliser `Objects.requireNonNull()`.

Exemple avec Spring `RestClient` :
```java
RestClient client = RestClient.builder()
               .baseUrl("https://example.org")
               .build();
String body = client.get().uri("/never-empty").retrieve().body(String.class);
System.out.println(Objects.requireNonNull(body).length);
```

Spring fournit aussi la classe Assert qui est reconnue par IntellijIDEA du fait de l'annotation `@Contract` : 
```java
Assert.state(body != null, "error message");

public abstract class Assert {
  @Contract("false, _ -> fail")
  public static void state(boolean expression, String message) {
    ...
  }
}

```

## Références

[Null Safety en Java avec JSpecify et NullAway - Sébastien Deleuze](https://youtu.be/F_Qyo1cCpvY?si=2HmbZbeiBk9i2GfJ)

https://jspecify.dev/

https://github.com/jspecify/jspecify

https://github.com/uber/NullAway

https://errorprone.info/index

## Introduction

Par défaut, un objet java est "nullable", provoquant potentiellement un NPE à l'exécution si non contrôlé. 

2 solutions pour éviter les NPE :

1/ L'API `Optional`. Inconvénients : invasive, non généralisable à tous les cas (concue principalement pour les valeurs de retour, cf. usage API Stream)

2/ Un set d'annotations reconnues par les outils comme JSR-305, Jetbrains, Spring, etc... \
**JSpecify** est une nouvelle librairie d'annotations qui si complétée de **NullAway** (optionnel), une extension **ErrorProne**, peut bloquer le build. Elle est standardisée et doit dévenir la référence à terme. Plus puissante, elle peut aussi annoter les objets d'un tableau ou les types paramétrés.  

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

JSpecify n'est utile qu'en dev avec un IDE. Pour détecter/bloquer lors du build, il faut installer un plugin **ErrorProne** comme **NullAway**.

Les dépendances à ajouter au build sont : `com.google.errorprone:error_prone_core:2.37.0`, `com.uber.nullaway:nullaway:0.12.6`.

Configuration du plugin errorprone :
- disableAllChecks : désactive les checks par défaut d'erronprone
- option [NullAway:OnlyNullMarked](https://github.com/uber/NullAway/wiki/Configuration#only-nullmarked-version-0123-and-after) à `true` : active nullaway
- error `NullAway` : bloque le build au lieu d'avertir
- option [NullAway:JSpecifyMode](https://github.com/uber/NullAway/wiki/Configuration#jspecify-mode) à `true`: indique que nullaway doit tenir compte des annotations jspecify
- option [NullAway:CustomContractAnnotation](https://github.com/uber/NullAway/wiki/Configuration#custom-contract-annotations) à eg. `org.sprigframework.lang.Contract` : facultatif, renseigne la(les) annotation(s) `@Contract` custom utilisée pour résoudre les NPE (ex. org.springframework.util.Assert.notNull)

Références : \
https://github.com/uber/NullAway/wiki/Configuration \
https://errorprone.info/docs/flags \
https://errorprone.info/docs/installation#maven

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

Spring fournit aussi la classe `org.springframework.util.Assert` également reconnue par IntellijIDEA du fait de l'annotation `@Contract` : 
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

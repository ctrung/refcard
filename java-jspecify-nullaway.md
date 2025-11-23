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
|`@NullMarked`|Positionné dans un package, indique que les objets sont `@NonNull` par défaut et annotés avec `@Nullable` explictement (inverse de java par défaut)|
|`@NullUnmarked`|Positionné dans un package, comportement par défaut java| 

Exempe `package-info.java` avec `@NullMarked` :
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
- option [NullAway:OnlyNullMarked](https://github.com/uber/NullAway/wiki/Configuration#only-nullmarked-version-0123-and-after) à `true` : active nullaway sur les packages @NullMarked (alternative : `-XepOpt:NullAway:AnnotatedPackages` pour désigner les packages à scanner)
- error `NullAway` : bloque le build au lieu d'avertir
- option [NullAway:JSpecifyMode](https://github.com/uber/NullAway/wiki/Configuration#jspecify-mode) à `true`: indique que nullaway doit tenir compte des annotations jspecify
- option [NullAway:CustomContractAnnotation](https://github.com/uber/NullAway/wiki/Configuration#custom-contract-annotations) à eg. `org.sprigframework.lang.Contract` : facultatif, renseigne la(les) annotation(s) `@Contract` custom utilisée pour résoudre les NPE (ex. org.springframework.util.Assert.notNull)

## Exemple configuration maven

Projet `.mvn/jvm.config`
```
--add-exports jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.main=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.model=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.processing=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED
--add-opens jdk.compiler/com.sun.tools.javac.code=ALL-UNNAMED
--add-opens jdk.compiler/com.sun.tools.javac.comp=ALL-UNNAMED
```

`pom.xml`
```xml
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <error-prone.version>2.44.0</error-prone.version>
        <jspecify.version>1.0.0</jspecify.version>
        <nullaway.version>0.12.12</nullaway.version>

        <maven-compiler-plugin.version>3.13.0</maven-compiler-plugin.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.jspecify</groupId>
            <artifactId>jspecify</artifactId>
            <version>${jspecify.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.version}</version>
                <configuration>
                    <source>${maven.compiler.source}</source>
                    <target>${maven.compiler.target}</target>
                    <encoding>UTF-8</encoding>
<!--                    <showWarnings>true</showWarnings>-->
                    <compilerArgs>
                        <arg>-XDcompilePolicy=simple</arg>
                        <arg>--should-stop=ifError=FLOW</arg>
                        <!-- autres opts : -XepDisableAllChecks, -XepOpt:NullAway:AnnotatedPackages=org.example -->
                        <!-- nécessaire pour java 21 (à partir de 21.0.8+, inutile java 22+) -->
                        <arg>-XDaddTypeAnnotationsToSymbol=true</arg>
                        <!-- NB : ordre des options important ! -->
                        <arg>
                            -Xplugin:ErrorProne \
                            -XepDisableAllChecks \
                            -Xep:NullAway:ERROR \
                            -XepOpt:NullAway:OnlyNullMarked=true \
                            -XepOpt:NullAway:JSpecifyMode=true
                        </arg>
                    </compilerArgs>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>com.google.errorprone</groupId>
                            <artifactId>error_prone_core</artifactId>
                            <version>${error-prone.version}</version>
                        </path>
                        <path>
                            <groupId>com.uber.nullaway</groupId>
                            <artifactId>nullaway</artifactId>
                            <version>${nullaway.version}</version>
                        </path>
                        <!-- Other annotation processors go here.

                        If 'annotationProcessorPaths' is set, processors will no longer be
                        discovered on the regular -classpath; see also 'Using Error Prone
                        together with other annotation processors' below. -->
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

Sortie console :
```
$ mvn clean verify
...
[INFO] --- compiler:3.13.0:compile (default-compile) @ spring ---
[INFO] Recompiling the module because of changed source code.
[INFO] Compiling 2 source files with javac [debug target 21] to target/classes
[INFO] -------------------------------------------------------------
[ERROR] COMPILATION ERROR : 
[INFO] -------------------------------------------------------------
[ERROR] /home/clement/info/projects/spring/src/main/java/org/example/Main.java:[11,40] [NullAway] dereferenced expression getMessage() is @Nullable
    (see http://t.uber.com/nullaway )
[INFO] 1 error
[INFO] -------------------------------------------------------------
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.960 s
[INFO] Finished at: 2025-11-23T12:47:42+01:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile (default-compile) on project spring: Compilation failure
[ERROR] /home/user/projects/spring/src/main/java/org/example/Main.java:[11,40] [NullAway] dereferenced expression getMessage() is @Nullable
[ERROR]     (see http://t.uber.com/nullaway )
[ERROR] 
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
```


Références : \
https://github.com/uber/NullAway/wiki/Configuration \
https://github.com/uber/NullAway/wiki/JSpecify-Support#supported-jdk-versions \
https://errorprone.info/docs/flags \
https://errorprone.info/docs/installation#maven

## Résolution des NPE

JSpecify indique une erreur si un test de nullité a été oublié pour une valeur `@Nullable`. 

Outre l'écriture d'un `if (object != null)`, on peut aussi : 

1/ Utilser `Objects.requireNonNull` du JDK

Exemple :

```java
RestClient client = RestClient.builder()
               .baseUrl("https://example.org")
               .build();
String body = client.get().uri("/never-empty").retrieve().body(String.class);
System.out.println(Objects.requireNonNull(body).length);
```

2/ Utiliser une méthode avec une annotation similaire à `@Contract` de Jetbrains, exemple avec `org.springframework.util.Assert` :

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

3/ Déclarer un faux positif

`@SuppressWarnings("NullAway")` sur une classe ou une méthode. \
`@SuppressWarnings("NullAway.Init")` sur une initialisation de champ oubliée.

```
public class PlayerRegistrar implements ApplicationEventPublisherAware {

  @SuppressWarnings("NullAway.Init")
  private ApplicationEventPublisher eventPublisher;

  ...

}
```

Doc : https://github.com/uber/NullAway/wiki/Suppressing-Warnings


## Références

[Null Safety en Java avec JSpecify et NullAway - Sébastien Deleuze](https://youtu.be/F_Qyo1cCpvY?si=2HmbZbeiBk9i2GfJ)

https://jspecify.dev/

https://github.com/jspecify/jspecify

https://github.com/uber/NullAway

https://errorprone.info/index

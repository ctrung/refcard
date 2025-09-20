
Guide des versions Java et nouvelles fonctionnalités (Marco Behler)

https://en.wikipedia.org/wiki/Java_version_history \
https://www.marcobehler.com/guides/a-guide-to-java-versions-and-features

## Java 1.2

- Java: the Collections Framework
- JIT compiler

## Java 1.4

- Java NIO (JSR 51)
- Logging API

## Java 5

- Generics
- Concurrent utils (JSR 166)
- For-each-loop
- Annotations
- `java.lang.instrument` package

## Java 6

- Additions and enhancements to the Java API (JSR 270)
- Parallel compaction for the use of multiple GC worker threads to perform compaction of the old generation
- `javax.script` package for scripting support
- Java Compiler API (JSR 199)
- JAXB 2.0 and JAX-WS 2.0

## Java 7

See Project Coin.

- Diamond op <>
- NIO.2
- fork/join framework (JSR 166)
- parallel GC became NUMA-aware
- Java SE 7 Update 4 introduced the Garbage First Garbage Collector (G1 GC)

## Java 8

- Lambdas, reference methods
- Default methods in interface
- Stream API
- Removal of permgen space \
  https://dzone.com/articles/java-8-permgen-metaspace
- String deduplication, feature designed for G1 (`-XX:+UseG1GC -XX:+UseStringDeduplication`)

## Java 9

Project Jigsaw.

- Java module system
- JShell, an interactive Java REPL (read–evaluate–print loop)
- Support for the Arm 64-bit architecture
- Improve Contended Locking ([JEP 143](https://openjdk.org/jeps/143))

### [JEP 247](https://openjdk.org/jeps/247) : release flag

Equivalent to `--source N` + `--target N` + additional checks on new Java API used.

Example
```java
jdk9/bin/javac --release 7 -d mylibrary <all sources except module-info>
jdk9/bin/javac --release 9 -d mylibrary src/module-info.java
```

### Multi-release jars

The manifest contains a `Multi-Release: true` entry.

Structure : 
```
mrlib.jar
├── META-INF
│   ├── MANIFEST.MF
│   └── versions
│       ├── 10
│       │   └── mrlib
│       │       └── Helper.class
│       └── 9
│           └── mrlib
│               └── Helper.class
└── mrlib
    ├── Helper.class
    └── Main.class
```

Command : 
```bash
javac --release 7 -d mrlib/7 src/<all top-level sources>      # Compile all normal sources at the desired minimum release level
javac --release 9 -d mrlib/9 src9/mrlib/Helper.java           # Compile the code only for Java 9 separately
jar -cfe mrlib.jar src/META-INF/MANIFEST.MF -C mrlib/7 .      # Create a JAR file with the correct manifest and the top-level classes
jar -uf mrlib.jar --release 9 -C mrlib/9 .                    # Update the JAR file with the new --release flag, which places class files in the correct META-INF/versions/9 directory
# continue with the JDK 10 version of the class 
```

- Because multi-release JARs are introduced with JDK 9, only 9 and up can be used under the versions directory. Any earlier JDKs will see only the top-level classes
- It’s a good idea to minimize the number of versioned classes. Factoring out the differences into a few classes decreases the maintenance burden. Versioning almost all classes in a JAR is undesirable
- Any class appearing under versions must also appear at the top level
- It’s fine to leave out Helper under versions/9, the fallback version is used

### `InputStream.readNBytes(byte[] b, int off, int len)`

https://stackoverflow.com/questions/53754387/java-read-vs-readnbytes-of-the-inputstream-instance

## Java 10

- Local Variable Type Inference (var, val)
- Improved the G1 GC by enabling parallel full garbage collections


## Java 11

- [Epsilon GC](https://blogs.oracle.com/javamagazine/post/epsilon-the-jdks-do-nothing-garbage-collector) : an experimental no-op GC designed to test the performance of applications with minimal GC interference
  (`java -XX:+UnlockExperimentalVMOptions -XX:+UseEpsilonGC -jar myApplication.jar`)
- `String.repeat(int)`
- Introduction of the experimental Z Garbage Collector (ZGC) : a low-latency, scalable GC designed to handle large heaps with minimal pause times
  (`java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -jar myApplication.jar`)
- new HTTP client API that supports HTTP/2 and WebSocket

## Java 12

- Introduction of the experimental Shenandoah GC. Like ZGC, Shenandoah GC is designed for large heaps with low latency requirements.
  (`java -XX:+UnlockExperimentalVMOptions -XX:+UseShenandoahGC -jar myApplication.jar`)
- Switch expressions (preview)
- `Collectors.teeing()`

## Java 13

- Multiline strings (preview)
- Enhancements to ZGC, eg. the new ZGC uncommit feature allowed unused heap memory to be returned to the operating system (OS) in a more timely and efficient manner

## Java 14

- Pattern matching for `instanceof` (preview)

### [JEP 361](https://openjdk.org/jeps/361) : Switch expressions

- *arrow syntax* pour éviter le *fall through*

Exemple : 

```java
final Boolean answer = switch(ch) {
    case 'T', 't' -> true;
    case 'F', 'f' -> false;
    default -> null;
};
```

## Java 15

- `String.formatted(Object... args)` : more readable than concatenation but significantly slower, so might be undesirable on hot code paths
- Sealed class (preview, [JEP 360](https://openjdk.org/jeps/360))
- Hidden classes ([JEP 371](https://openjdk.org/jeps/371))

### [JEP 378](https://openjdk.org/jeps/378) : Text blocks

* First line must be at next line after three double quotes (""")
* Incidental spaces vs essential spaces
* `\` at the end of a line to join with next line (ie. omits newline on that line)
* `\"""` escapes a three double quotes (`\"\"\"` works too)

```java
String text = """
                Lorem ipsum dolor sit amet, consectetur adipiscing \
                elit, sed do eiusmod tempor incididunt ut labore \
                et dolore magna aliqua.""";
```

## Java 16

https://blogs.oracle.com/javamagazine/post/the-hidden-gems-in-java-16-and-java-17-from-streammapmulti-to-hexformat

- ZGC enhancements ([JEP 376](https://openjdk.org/jeps/376))
- `Stream.mapMulti()`
- Les membres static sont désormais autorisés dans les inner classes, principalement pour autoriser les nested record qui sont implicitement static.

### [JEP 394](https://openjdk.org/jeps/394) : Pattern matching for `instanceof`

```java
if (obj instanceof First first) {
    return checkFirst(first);
}
```

### [JEP 395](https://openjdk.org/jeps/395) : Records

## Java 17

https://blogs.oracle.com/javamagazine/post/the-hidden-gems-in-java-16-and-java-17-from-streammapmulti-to-hexformat

- [`java.util.HexFormat`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HexFormat.html) class
- Enhanced Pseudo-Random Number Generators ([JEP 356](https://openjdk.org/jeps/356))
- Strongly Encapsulate JDK Internals ([JEP 403](https://openjdk.org/jeps/403))
- Pattern matching for switch (preview, [JEP 406](https://openjdk.org/jeps/406))
- Foreign Function and Memory API (Incubator, [JEP 412](https://openjdk.org/jeps/412))
- Applet API deprecated ([JEP 398](https://openjdk.org/jeps/398)), Security Manager deprecated for Removal ([JEP 411](https://openjdk.org/jeps/411))

## Java 21

https://blogs.oracle.com/javamagazine/post/java-21-now-available

### [JEP 453](https://openjdk.org/jeps/453) : Structured concurrency

Définition wiki : https://en.wikipedia.org/wiki/Structured_concurrency \
Concurrency Java 21 : https://docs.oracle.com/en/java/javase/21/core/concurrency.html \
Structured Concurrency in modern Java : https://youtube.com/playlist?list=PLZ9NgFYEMxp6-DE4NiIE2K1RBrfRxK_XC&si=XDBO44SZG7Cfzsxf \
Discover Structured Concurrency : https://theboreddev.com/understanding-structured-concurrency

### [JEP 431](https://openjdk.org/jeps/431) : Sequenced collections

Stuart Marks (JDK Core Libraries project lead) à Devoxx Belgique 👍 \
https://www.youtube.com/watch?v=fdFM5CFqv6o

Nouvelles interfaces `SequencedCollection`, `SequencedSet`, `SequencedMap` pour uniformiser la notion d'ordre/de séquence au sein de l'API Collection (premier, dernier, vue inversée).

![image](https://github.com/user-attachments/assets/35344cdd-5480-47de-85d2-a431b2515a37)

![image](https://github.com/user-attachments/assets/f0e22564-4ae9-4e94-9b01-29c72f2bc782)

Exemple, itérer une liste en arrière sans passer par un `ListIterator` : `liste.reversed()`


### [JEP 441](https://openjdk.org/jeps/441) : Pattern matching for `switch`

TL;DR
```java
// Prior to Java 21
static String formatter(Object obj) {
    String formatted = "unknown";
    if (obj instanceof Integer i) {
        formatted = String.format("int %d", i);
    } else if (obj instanceof Long l) {
        formatted = String.format("long %d", l);
    } else if (obj instanceof Double d) {
        formatted = String.format("double %f", d);
    } else if (obj instanceof String s) {
        formatted = String.format("String %s", s);
    }
    return formatted;
}

// As of Java 21
static String formatterPatternSwitch(Object obj) {
    return switch (obj) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> obj.toString();
    };
}
```

## Java 22

### [JEP-447](https://openjdk.org/jeps/447) : Déclarations avant super()

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



### [JEP 456](https://openjdk.org/jeps/456) : Variables et modèles sans noms

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

## Java 24 (19 mars 2025)

### Stream gatherers [JEP 485](https://openjdk.org/jeps/485)

API générique pour implémenter une opération intermédiaire au sein de l'API Stream (équivalent à l'API Collector pour les opérations finales).
Supporte un état, la transformation et le parallélisme. 

Composant d'un gatherer : initializer, integrator, combiner (optionnel, pour le //), finisher

Gatherers déjà implémentés dans le JDK :
- [fold](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/stream/Gatherers.html#fold(java.util.function.Supplier,java.util.function.BiFunction)) : réduction ordonnée, many-to-one
- [mapConcurrent](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/stream/Gatherers.html#mapConcurrent(int,java.util.function.Function)) : transformation concurrente avec un nombre fini de virtual threads, one-to-one
- [scan](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/stream/Gatherers.html#scan(java.util.function.Supplier,java.util.function.BiFunction)) : accumulation incrémentale, one-to-one
- [windowFixed](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/stream/Gatherers.html#windowFixed(int)) : groupe par taille définie, many-to-many
- [windowSliding](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/util/stream/Gatherers.html#windowSliding(int)) : groupe par taille définie et sur une fenêtre glissante, many-to-many

Actions sur les gatherers :
- composition avec `andThen`

Exemples : 

Identifier les couples adjacents selon un critère déterminé  
```java
List<List<Reading>> findSuspicious(Stream<Reading> source) {
    return source.gather(Gatherers.windowSliding(2))
                 .filter(window -> (window.size() == 2
                                    && isSuspicious(window.get(0),
                                                    window.get(1))))
                 .toList();
}
```
Pour créer son propre gatherer :
- Implémenter `Gatherer`
- Utiliser `Gatherer.ofSequential(...)` (adhoc)

Autres lectures : https://cr.openjdk.org/~vklang/Gatherers.html

## Java 25

[Oracle Press Releases Java 25](https://www.oracle.com/news/announcement/oracle-releases-java-25-2025-09-16/)

[Soirée Paris JUG 9 sept 2025](https://www.parisjug.org/events/2025/09-16-java-25/) 

### Scoped Values - [JEP 506](https://openjdk.org/jeps/506)

Alternative performante de ThreadLocal dans le cas classique d'un scénario où on cherche à récupérer la valeur issue d'une frame parente dans la pile d'exécution.

### Stable Values - [JEP 502](https://openjdk.org/jeps/502) (Preview)

API pour le [DCL](https://en.wikipedia.org/wiki/Double-checked_locking) qui est dur à implémenter correctement. Usage principal : économiser le coût du synchronized dans 99% des cas quand on veut implémenter du loazy loding ou le pattern singleton.

### Virtual Threads

Réécriture du synchronized en java 24 pour éviter d'épingler un thread virtuel.

### Compact Object Headers - [JEP 519](https://openjdk.org/jeps/519)

Fait parti du projet Lilliput v1

Diminuer le header des objets de 96 à 64 octets. Option expérimentale qui fonctionne déjà car en place par défaut chez Amazon.

### Primitive Types in Patterns, instanceof, and switch - [JEP 507](https://openjdk.org/jeps/507) (Third Preview)

### Flexible Constructor Bodies - [JEP 513](https://openjdk.org/jeps/513)

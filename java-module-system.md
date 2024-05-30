## Reference materials

**Videos** \
Talk at JavaOne 2017 by Alex Buckley : https://www.youtube.com/watch?v=gtcTftvj0d0 \
Quick summary by Alex Buckley :        https://www.youtube.com/watch?v=22OW5t_Mbnk

**Books** \
Java 9 Modularity by Sander Mak & Paul Bakker (O'Reilly) \
Java Application Architecture by Kirk Knoernschild (Prentice Hall)

## Specifications

- JEP 261 : Module System
- JEP 260 : Encapsulate Most Internal APIs
- JEP 223 : New Version String Scheme
- JEP 220 : Modular Run-Time Images
- JEP 200 : The Modular JDK

## Definition

1. Set of packages designed for reuse.
Overcome the limitations of packages and existing classes modifiers (eg. public)
Strong encapsulation (exported vs concealed packages)

module-info.java
```java
module java.base {
	exports java.lang;
	exports java.io;
	exports java.net;
	exports java.util;	
}
```

`javac -d classes -sourcepath src module-info.java com/example/hello/SayHello.java`

-> modular jars \
-> module path

NB : Qualified exports -> exports to friend modules
```java
module API {
	exports P;
	exports P.Helper to Impl1, Impl2; 
}
```

2. Reliable dependencies.

A module is also a set of packages exported by other modules 

```java
module hello.world {
	exports com.example.hello;
	requires java.base;	
}
```

3. Benefits of modules 
* Run with java -p mods -m hello.world
* No missing dependencies
* No cyclic dependencies
* No split packages
* Modules are not mandatory - classpath still exists !

## Migrating to Java 9

* Java EE modules are deprecated for removal (use `--add-modules`)
  - java.activation
  - java.corba
  - java.transaction
  - java.xml.bind
  - java.xml.ws
  - java.xml.ws.annotation
* Illegal access to JDK internals causes runtime warnings (use `--add-opens`)


Running jdeps to identify dependencies : 
```sh
$ jdeps -s lib/myapp.jar lib/mylib.jar
myapp.jar -> lib/jackson-core-2.6.2.jar
myapp.jar -> lib/jackson-databind-2.6.2.jar
myapp.jar -> mylib.jar
myapp.jar -> java.base
myapp.jar -> java.sql
mylib.jar -> java.base
```

For libraries maintainers : `jdeps -jdkinternals library.jar` on JDK 8 or 9 


Automatic modules to ease migration and use non modular jars (yesterday's JARs are today's modules) : 
* "Real" modules
* No changes to someone else's JAR file
* Module name derived from JAR file name (hyphens to dots, no version)
* Exports all its packages
* Requires all other modules

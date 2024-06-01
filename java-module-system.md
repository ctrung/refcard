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

Transitive exports to ease the number of requires by a consumer module
```java
module java.sql {
  requires transitive java.logging; 
  requires transitive java.xml;
  ...
}

module hello.world {
  ...
  requires java.sql;   // modules java.logging and java.xml are automatically required by transitivity
}
```
Rule : use `requires transitive` if these types appears in your exported package API signatures 

3. Benefits of modules 
* Reliable configuration : dependencies are checked at compilation and runtime reducing runtime errors
* Strong encapsulation : explicit exposition, internal are concealed  
* Scalable development : explicit boundaries allow for maintanable and parallel work
* Security : strong cncapsulation reduces surface attacks
* Optimization : class loading is more performant during JVM startup 

For examples : 
* Run with `java -p mods -m hello.world`
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


**Scenario 1** : Modular app + un-modularized libs

-> Automatic modules to ease migration and use non modular jars (yesterday's JARs are today's modules) : 
* "Real" modules
* No changes to someone else's JAR file
* Module name derived from JAR file name (hyphens to dots, no version)
* Exports all its packages
* Requires all other modules

**Scenario 2** : Un-modularized app

-> Unnamed module to make not yet modular code (eg. our app) continue running on JDK 9. Unamed modules access all modules. 


## Linking

**New !!** : Optional phase between compilation and runtime. 

A new tool called `jlink` can create a runtime image containing only the necessary modules to run an application.

`jlink --module-path mods/:$JAVA_HOME/jmods --add-modules hello --launcher hello=hello --output hello-image`

`jlink` options : 
* `--module-path`
* `--add-modules` : root module that needs to be runnable in the runtime image
* `--launcher <name>=<module>[/<mainclass>]` : Add a launcher command (aka. script in the bin folder) of the given name for the module and the main class if specified

What is produced ? 

```
hello-image/
├── bin
│   ├── hello    <<<< --launcher option
│   ├── java     <<<< java and resolved modules are embedded  
│   └── keytool
├── conf
│   ├── net.properties
│   └── security
│       ├── java.policy
│       ├── java.security
│       └── policy
│           ├── limited
│           │   ├── default_local.policy
│           │   ├── default_US_export.policy
│           │   └── exempt_local.policy
│           ├── README.txt
│           └── unlimited
│               ├── default_local.policy
│               └── default_US_export.policy
├── include
│   ├── classfile_constants.h
│   ├── jni.h
│   ├── jvmticmlr.h
│   ├── jvmti.h
│   └── linux
│       └── jni_md.h
├── legal
│   └── java.base
│       ├── ADDITIONAL_LICENSE_INFO
│       ├── aes.md
│       ├── asm.md
│       ├── ASSEMBLY_EXCEPTION
│       ├── cldr.md
│       ├── c-libutl.md
│       ├── icu.md
│       ├── LICENSE
│       ├── public_suffix.md
│       └── unicode.md
├── lib
│   ├── classlist
│   ├── jexec
│   ├── jli
│   │   └── libjli.so
│   ├── jrt-fs.jar
│   ├── jspawnhelper
│   ├── jvm.cfg
│   ├── libjava.so
│   ├── libjimage.so
│   ├── libjsig.so
│   ├── libnet.so
│   ├── libnio.so
│   ├── libverify.so
│   ├── libzip.so
│   ├── modules
│   ├── security
│   │   ├── blocked.certs
│   │   ├── cacerts
│   │   ├── default.policy
│   │   └── public_suffix_list.dat
│   ├── server
│   │   ├── libjsig.so
│   │   ├── libjvm.so
│   │   └── Xusage.txt
│   └── tzdb.dat
└── release
```


## Commands

`javac` options :
* `-d` : destination

`jar` options :
* `-c` : create
* `-f` : file
* `-e` : entry point (default main class)
* `-C` : change

`java` options :
* `-p` : `--module-path`, module path
* `-m` : `--module`, module name

```
java --list-modules   # list platform modules

javac -d out/helloworld src/helloworld/com/javamodularity/helloworld/HelloWorld.java src/helloworld/module-info.java  # NB : module name must match with root folder name !

jar -cfe mods/helloworld.jar com.javamodularity.helloworld.HelloWorld -C out/helloworld

java -p out/ -m hello/com.hello.HelloWorld  # exploded module mode, module name option always comes last and has syntax `module-name`/`main class`
java -p mods -m hello/com.hello.HelloWorld  # modular jar mode (explicit main class)
java -p mods -m hello                       # modulat jar mode (implicit main class)

java --show-module-resolution --limit-modules java.base -p mods -m hello                       # trace module resolution, --limit-modules prevents other platform modules from being resolved through service binding
java -Xlog:module=debug --show-module-resolution --limit-modules java.base -p mods -m hello    # more verbose...



```

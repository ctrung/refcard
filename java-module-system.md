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

**NB 1** : Use `requires transitive` if these types appears in your exported package API signatures (return types, argument types, exceptions, etc...).

**NB 2** : When compiling with the `-Xlint:exports` flag, any types that are part of exported types which are not transitively required (but should be) result in a warning.



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

```sh
jlink --module-path mods/:$JAVA_HOME/jmods --add-modules hello --launcher hello=hello --output hello-image

jlink --module-path mods/:$JAVA_HOME/jmods --add-modules easytext.cli --add-modules easytext.analysis.coleman --add-modules easytext.analysis.kincaid --add-modules easytext.analysis.naivesyllablecounter --output image  # adding optional service provider modules and its transitive deps

```

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
* `-m` : `--module`, Compile only the specified module, check timestamps
* `-p` : `--module-path`, Specify where to find application modules
* `--module-source-path` : Specify where to find input source files for multiple modules

`jar` options :
* `-c` : create
* `-f` : file
* `-e` : entry point (default main class)
* `-C` : change
* `--module-version=<V>` : set module version

`java` options :
* `-p` : `--module-path`, module path
* `-m` : `--module`, module name
* `--add-opens <module>/<package>=<targetmodule>` : add opens

```
java --list-modules              # list platform modules
java --describe-module java.se   # show module descriptor

javac -d out/helloworld src/helloworld/com/javamodularity/helloworld/HelloWorld.java src/helloworld/module-info.java  # NB : module name must match with root folder name !
javac -d out --module-source-path src -m easytext.cli

jar -cfe mods/helloworld.jar com.javamodularity.helloworld.HelloWorld -C out/helloworld

java -p out/ -m hello/com.hello.HelloWorld  # exploded module mode, module name option always comes last and has syntax `module-name`/`main class`
java -p mods -m hello/com.hello.HelloWorld  # modular jar mode (explicit main class)
java -p mods -m hello                       # modulat jar mode (implicit main class)

java --show-module-resolution --limit-modules java.base -p mods -m hello                       # trace module resolution, --limit-modules prevents other platform modules from being resolved through service binding
java -Xlog:module=debug --show-module-resolution --limit-modules java.base -p mods -m hello    # more verbose...

```

### Services

Optional feature for creating modular codebase, aka. decoupling modules. \
The issue to solve is to program to interfaces without tight coupling to implementations. 

This optional feature of the JPMS is based on the `ServiceLoader` API which provides implementation at runtime.

`Provider side`

```java

// declaration inside module-info.java

module easytext.analysis.coleman {

  requires easytext.analysis.api;

  provides javamodularity.easytext.analysis.api.Analyzer
    with javamodularity.easytext.analysis.coleman.ColemanAnalyzer;     // <----
}
```

`*Consumer side*`

```java

// step 1 : declaration inside module-info.java

module easytext.cli {

  requires easytext.analysis.api;

  uses javamodularity.easytext.analysis.api.Analyzer;                   // <---- No Analyzer implementation need to be available at compile-time !
}


// step 2 : inside consumer code

Iterable<Analyzer> analyzers = ServiceLoader.load(Analyzer.class);

for (Analyzer analyzer: analyzers) {
  System.out.println(analyzer.getName() + ": " + analyzer.analyze(sentences));
}

```

Remarks : 
- Services retrieved through `ServiceLoader` are lazily fetched
- Services retrieved through one same `ServiceLoader` instance are cached (use `reload()` to refresh)
- `ServiceLoader`s are not singletons (aka shared inside the JVM)
- Service provider methods : public no arg contructor or public static `provider()` method
- Static provider method can be implemented in a separate class that need be declared in `module-info.java` (thus not the implementation that can be package private then) : `provides javamodularity.easytext.analysis.api.Analyzer with javamodularity.providers.factory.ExampleProviderFactory;`
- No shutdown or service deregistration (deletion through garbage collection)
- To avoid calling `ServiceLoader` API in consumer class, one can use the *factory pattern* by adding a java 8 static method in the service interface : `static Iterable<Analyzer> getAnalyzers() { return ServiceLoader.load(Analyzer.class); }` and declaring the `uses` constraint in the `module-info.java` file of the api  module : `uses javamodularity.easytext.analysis.api.Analyzer;`
- Service type inspection is provided through the `public static Stream<ServiceLoader.Provider<S>> ServiceLoader.stream​()` API
- Service providers are resolved during runtime startup based on the `provides` and `uses` clauses. If a required dependency of a provider module is not found, execution is stopped. No provider services is possible and does not bloc app execution.
- `jlink` does not do automatic service binding for many reasons : it's optional, it depends on the consumer side configuration, and images built with jlink might become big since `java.base` relies a lot on services.

## Aggregator module

Module that only requires transitively other deps, no code exported. Example java.se :

```sh
java --describe-module java.se
java.se@11.0.18
requires java.management.rmi transitive
requires java.sql.rowset transitive
requires java.instrument transitive
requires java.desktop transitive
requires java.transaction.xa transitive
requires java.security.jgss transitive
requires java.management transitive
requires java.prefs transitive
requires java.security.sasl transitive
requires java.rmi transitive
requires java.naming transitive
requires java.datatransfer transitive
requires java.base mandated
requires java.xml.crypto transitive
requires java.xml transitive
requires java.scripting transitive
requires java.logging transitive
requires java.compiler transitive
requires java.net.http transitive
requires java.sql transitive
```

## Requires static

### Optional dependencies

Modules that are optional at runtime (aka optional dependencies) can be implemented  with services through the `ServiceLoader` API or with the `requires static` clause.

```java
module framework {
  requires static fastjsonlib;    <--- module system checks this at compile time, not at runtime
}
```

**NB** : the `require static` does not add dependencies for resolution at runtime even if these are in the module path ! For this, one need to have a direct `requires` clause or use `--add-modules` to add it as a root module. 

### Reference compile time only annotations

Example : checking @Nullable or @NonNull or generating code during compilation. 

## Resources

https://github.com/java9-modularity/examples/blob/master/chapter5/resource_encapsulation 

With modules, resources within packages in a module are strongly encapsulated (opposite of java <= 8 with classpath).
Resource encapsulation applies only to resources in packages...

```java
Class clazz = ResourcesInModule.class;
InputStream cz_pkg = clazz.getResourceAsStream("resource_in_package.txt");
URL cz_tl = clazz.getResource("/top_level_resource.txt");

Module m = clazz.getModule();
InputStream m_pkg = m.getResourceAsStream("javamodularity/firstresourcemodule/resource_in_package.txt");
InputStream m_tl = m.getResourceAsStream("top_level_resource.txt");
assert Stream.of(cz_pkg, cz_tl, m_pkg, m_tl)
             .noneMatch(Objects::isNull);
}
```

```java
Optional<Module> otherModule = ModuleLayer.boot().findModule("secondresourcemodule"); //<1>

otherModule.ifPresent(other -> {
   try {
      InputStream m_tl = other.getResourceAsStream("top_level_resource2.txt"); //<2>
      InputStream m_pkg = other.getResourceAsStream(
          "javamodularity/secondresourcemodule/resource_in_package2.txt"); //<3>
      InputStream m_class = other.getResourceAsStream(
          "javamodularity/secondresourcemodule/A.class"); //<4>
      InputStream m_meta = other.getResourceAsStream("META-INF/resource_in_metainf.txt"); //<5>
      InputStream cz_pkg =
        Class.forName("javamodularity.secondresourcemodule.A")
             .getResourceAsStream("resource_in_package2.txt"); //<6>

      assert Stream.of(m_tl, m_class, m_meta)
                   .noneMatch(Objects::isNull);
      assert Stream.of(m_pkg, cz_pkg)
                   .allMatch(Objects::isNull);
```

## ResourceBundles

JDK 9+ relies on services to expose resource bundles.

See https://github.com/java9-modularity/examples/tree/master/chapter5/resourcebundles for an example.

## Open modules and packages

From Java 9+, the module system doesn't allow access to internal parts at runtime (strong encapsulation principle). \
In practice, accessing inner parts of reflective objects with `setAccessible()` is forbidden whereas it was allowed before java 9. 

Problem : reflection based frameworks and libraries (eg. serialisation API, Spring dependencies injection, Hibernate ORM, etc...) need this feature for backward compatibility. \
Two aspects here : 
1. Access internal types without exporting packages
1. Allow reflective access to all parts of these types

Open modules and packages address the second point.

`module-info.java`
```java

// open the whole module
open module deepreflection {
  exports api;
}

// open a package
module deepreflection {
  exports api;
  opens internal;
}


// qualified open : open a package selectively
module deepreflection {
  exports api;
  opens internal to library;
}
```

To open a third party or platform module, the `java` command supports the `--add-opens <module>/<package>=<targetmodule>` option.

**NB** : Java 9 offers a better alternative ([JEP 193](https://openjdk.org/jeps/193)) : MethodHandles and VarHandles, which is a more principled and
performance-friendly approach to access application internals. In time, frameworks and libraries will migrate and use this alternative in the future. 


## `Module` API

### Instrospection

 ```java

// Access to the Module class through the Class class
Module module = String.class.getModule();

String name1 = module.getName(); // Name as defined in module-info.java
Set<String> packages1 = module.getPackages(); // Lists all packages in the module

ModuleDescriptor descriptor = module.getDescriptor();

String name2 = descriptor.name(); // Same as module.getName();
Set<String> packages2 = descriptor.packages(); // Same as module.getPackages();

// Through ModuleDescriptor, all information from module-info.java is exposed:
Set<Exports> exports = descriptor.exports(); // All exports, possibly qualified
Set<String> uses = descriptor.uses(); // All services used by this module
```

### Modifying a module

```java
Module target = ...; // Somehow obtain the module you want to export to
Module module = getClass().getModule(); // Get the module of the current class
module.addExports("javamodularity.export.atruntime", target);
```

Adding a module export **from another module** is not possible by the java module system (no escalation).

Four methods : 
- `addExports(String packageName, Module target)` : Expose previously nonexported packages to another module.
- `addOpens(String packageName, Module target)`   : Opens a package for deep reflection to another module.
- `addReads(Module other)`                        : Adds a reads relation from the current module to another module.
- `addUses(Class<?> serviceType)`                 : Indicates that the current module wants to use additional service types with ServiceLoader.

### Migration

Classpath still cohabits with modulpath for non modularized applications and libraries.

From Java 9+, classpath based apps will see messages like **WARNING: An illegal reflective access operation has occurred**. 
Options to customize this behaviour (can't suppress) : 
- --illegal-access=permit : only shows at the first access
- --illegal-access=warn   : shows at each access
- --illegal-access=debug  : add debug stacktraces
- --illegal-access=deny   : forbids illegal access, default behaviour after JDK 17+

**PS** : This only concerns old unencapsulated API before Java 9. New encapsulated API access are forbidden by default, whether on not on JDK 9~17. 

To (exceptionnaly) allow illegal accesses, use : `java --add-opens java.base/java.lang=ALL-UNNAMED` (ALL-UNAMED represents the classpath).


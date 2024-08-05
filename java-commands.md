## `jdeprscan`

Analyzes the usage of deprecated APIs in modules.

```sh
$ jdeprscan --for-removal mods/com.codekaram.brickhouse
No deprecated API marked for removal found

$ jdeprscan --list mods/com.codekaram.brickhouse
No deprecated API found.
```

## `jdeps`

A tool that facilitates analysis of the dependencies of Java classes or packages.

```sh
$ jdeps mods/com.codekaram.brickhouse/com/codekaram/brickhouse/loadLayers.class
$ jdeps -v mods/com.codekaram.brickhouse/com/codekaram/brickhouse/loadLayers.class
```

## `jlink`

A tool that helps link modules and their transitive dependencies to create custom modular runtime images. 
These custom images can be packaged and deployed without needing the entire Java Runtime Environment (JRE), 
which makes your application lighter and faster to start.

```sh
# --bind-services : module is a consumer of services, it’s necessary to link the service providers and their dependencies
$ jlink --module-path $JAVA_HOME/jmods:build/modules --add-modules com.example.builder --output consumer.services --bind-services

# run
$ consumer.services/bin/java -m com.example.builder/com.example.builder.Builder
```

## `jmod`

A tool used to create, describe, and list JMOD files. JMOD files are an alternative to JAR files for packaging modular Java applications, 
which offer additional features such as native code and configuration files. These files can be used for distribution or to create custom runtime images with jlink.

```sh
# create
$ javac --module-source-path src -d build/modules $(find src -name "*.java")
$ jmod create --class-path build/modules/com.codekaram.brickhouse com.codekaram.brickhouse.jmod

# describe
$ jmod describe com.codekaram.brickhouse.jmod

# list content
$ jmod list com.codekaram.brickhouse.jmod
```

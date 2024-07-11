## Java 9

### JEP 247 : release flag

Equivalent to `--source N` + `--target N` + additional checks on Java API used.

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



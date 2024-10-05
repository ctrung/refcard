## `jar`

```sh
jar -­cvf myNewFile.jar .
jar -­-­create -­-­verbose -­-­file myNewFile.jar .   # dernier arg. : arborescence à mettre dans le jar

jar -­cvf myNewFile.jar -­C dir .                 # -C : change directory
```

## `java`

```sh
# `Bronx Zoo` are two command line args to the Zoo program
java Zoo      Bronx Zoo   # Executes Zoo.class
java Zoo.java Bronx Zoo   # JEP 330: Launch Single-File Source-Code Programs

java -­cp classes packageb.ClassB                                         # -cp, -classpath, --class-path are all equivalent
java -­cp ".:/tmp/someOtherLocation:/tmp/myJar.jar" myPackage.MyClass
```

## `javac`

```sh
javac packagea/ClassA.java packageb/ClassB.java               # each compiled class is near its java file 
javac -­d classes packagea/ClassA.java packageb/ClassB.java    # all compiled classes hierarchy in a same directory 
```

## `jcmd`

Logging, see [`-Xlog`](/java-options.md#-xlog-java-9) option for more details
```sh
# dynamic logging (since Java 11)
jcmd <pid> VM.log what=gc*=trace decorators=uptimemillis,tid,hostname
# current config and available options 
jcmd <pid> help VM.log
```

Thread dump and deadlocks if any, [see `jstack <pid>` for deadlock example due to circular initialization order](#jstack).
```sh
jcmd <pid> Thread.print
```

## `jconsole`

Console java (client lourd) pour monitorer un process Java.

```sh
jconsole
jconsole <pid>
```

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

## `jps`

List all java processes.

```sh
$ jps
30915 Launcher
31061 Jps
30918 HundredMistakes
5084 Main
```

## `jstack`

Threads dump and deadlocks if any, equivalent to [`jcmd <pid> Thread.print`](#jcmd).

`jstack <pid>`

<details>
  <summary>Example of deadlock due to circular initialization order (100 Java mistakes - chap 3.9.3)</summary>

```java
public class Deadlock {

    public static void main(String[] args) {
        Runnable[] actions = {() -> new A(), () -> new B()};
        Stream.of(actions).parallel().forEach(Runnable::run);
        System.out.println("ok");
    }

    static class A {
        private static final B unused = new B();
        static final String WELCOME = "Hello".toUpperCase();
        public static void main(String[] args) {
            System.out.println(B.MSG);
        }
    }

    static class B {
        static final String MSG = A.WELCOME;
        public static void main(String[] args) {
            System.out.println(B.MSG);
        }
    }

}
```

```sh
$ jstack 12608
12608:
2024-10-05 11:05:38
Full thread dump OpenJDK 64-Bit Server VM (17.0.12+7 mixed mode, sharing):

Threads class SMR info:
_java_thread_list=0x00007f014c126340, length=14, elements={
0x00007f0210029760, 0x00007f021018a380, 0x00007f021018b770, 0x00007f0210190f20,
0x00007f02101922e0, 0x00007f0210193700, 0x00007f02101950c0, 0x00007f0210196600,
0x00007f021019fa70, 0x00007f02101ab240, 0x00007f021031c6a0, 0x00007f021031d490,
0x00007f0210324b40, 0x00007f017c000e70
}

"main" #1 prio=5 os_prio=0 cpu=58,33ms elapsed=435,63s tid=0x00007f0210029760 nid=0x3141 in Object.wait()  [0x00007f0217608000]
   java.lang.Thread.State: RUNNABLE
	at com.example.spring_modulith.deadlock.Deadlock$B.<clinit>(Deadlock.java:22)
	- waiting on the Class initialization monitor for com.example.spring_modulith.deadlock.Deadlock$A
	at com.example.spring_modulith.deadlock.Deadlock.lambda$main$1(Deadlock.java:8)
	at com.example.spring_modulith.deadlock.Deadlock$$Lambda$15/0x00007f0194001428.run(Unknown Source)
	at com.example.spring_modulith.deadlock.Deadlock$$Lambda$17/0x00007f0194002000.accept(Unknown Source)
	at java.util.stream.ForEachOps$ForEachOp$OfRef.accept(java.base@17.0.12/ForEachOps.java:183)
	at java.util.Spliterators$ArraySpliterator.forEachRemaining(java.base@17.0.12/Spliterators.java:992)
	at java.util.stream.AbstractPipeline.copyInto(java.base@17.0.12/AbstractPipeline.java:509)
	at java.util.stream.ForEachOps$ForEachTask.compute(java.base@17.0.12/ForEachOps.java:290)
	at java.util.concurrent.CountedCompleter.exec(java.base@17.0.12/CountedCompleter.java:754)
	at java.util.concurrent.ForkJoinTask.doExec(java.base@17.0.12/ForkJoinTask.java:373)
	at java.util.concurrent.ForkJoinTask.invoke(java.base@17.0.12/ForkJoinTask.java:686)
	at java.util.stream.ForEachOps$ForEachOp.evaluateParallel(java.base@17.0.12/ForEachOps.java:159)
	at java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateParallel(java.base@17.0.12/ForEachOps.java:173)
	at java.util.stream.AbstractPipeline.evaluate(java.base@17.0.12/AbstractPipeline.java:233)
	at java.util.stream.ReferencePipeline.forEach(java.base@17.0.12/ReferencePipeline.java:596)
	at java.util.stream.ReferencePipeline$Head.forEach(java.base@17.0.12/ReferencePipeline.java:765)
	at com.example.spring_modulith.deadlock.Deadlock.main(Deadlock.java:9)
...
"ForkJoinPool.commonPool-worker-1" #15 daemon prio=5 os_prio=0 cpu=0,48ms elapsed=435,57s tid=0x00007f0210324b40 nid=0x3161 in Object.wait()  [0x00007f01f08bf000]
   java.lang.Thread.State: RUNNABLE
	at com.example.spring_modulith.deadlock.Deadlock$A.<clinit>(Deadlock.java:14)
	- waiting on the Class initialization monitor for com.example.spring_modulith.deadlock.Deadlock$B
	at com.example.spring_modulith.deadlock.Deadlock.lambda$main$0(Deadlock.java:8)
	at com.example.spring_modulith.deadlock.Deadlock$$Lambda$14/0x00007f0194001208.run(Unknown Source)
	at com.example.spring_modulith.deadlock.Deadlock$$Lambda$17/0x00007f0194002000.accept(Unknown Source)
	at java.util.stream.ForEachOps$ForEachOp$OfRef.accept(java.base@17.0.12/ForEachOps.java:183)
	at java.util.Spliterators$ArraySpliterator.forEachRemaining(java.base@17.0.12/Spliterators.java:992)
	at java.util.stream.AbstractPipeline.copyInto(java.base@17.0.12/AbstractPipeline.java:509)
	at java.util.stream.ForEachOps$ForEachTask.compute(java.base@17.0.12/ForEachOps.java:290)
	at java.util.concurrent.CountedCompleter.exec(java.base@17.0.12/CountedCompleter.java:754)
	at java.util.concurrent.ForkJoinTask.doExec(java.base@17.0.12/ForkJoinTask.java:373)
	at java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(java.base@17.0.12/ForkJoinPool.java:1182)
	at java.util.concurrent.ForkJoinPool.scan(java.base@17.0.12/ForkJoinPool.java:1655)
	at java.util.concurrent.ForkJoinPool.runWorker(java.base@17.0.12/ForkJoinPool.java:1622)
	at java.util.concurrent.ForkJoinWorkerThread.run(java.base@17.0.12/ForkJoinWorkerThread.java:165)
...
```
</details>

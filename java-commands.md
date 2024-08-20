## `jcmd`

Logging, see [`-Xlog`](/java-options.md#-xlog-java-9) option for more details
```sh
# dynamic logging (since Java 11)
jcmd <pid> VM.log what=gc*=trace decorators=uptimemillis,tid,hostname
# current config and available options 
jcmd <pid> help VM.log
```

Thread dump (and deadlocks if present)
```sh
# equivalent to jstack <pid>
jcmd <pid> Thread.print
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

Threads dump and deadlocks if presents (eq. to `jcmd <pid> Thread.print`).

```sh
$ jstack 30918
2024-08-20 21:25:06
Full thread dump OpenJDK 64-Bit Server VM (22.0.2+9-70 mixed mode, sharing):

Threads class SMR info:
_java_thread_list=0x00007feee4000d40, length=14, elements={
0x00007fef88176510, 0x00007fef88177b60, 0x00007fef88179640, 0x00007fef8817ac80,
0x00007fef8817c220, 0x00007fef8817dd40, 0x00007fef88187440, 0x00007fef881a16e0,
0x00007fef886c01b0, 0x00007fef886c0b20, 0x00007fef886cf9e0, 0x00007fef886d0b20,
0x00007fef88030650, 0x00007feefc000e70
}

"Reference Handler" #9 [30927] daemon prio=10 os_prio=0 cpu=0,12ms elapsed=28,22s tid=0x00007fef88176510 nid=30927 waiting on condition  [0x00007fef68e56000]
   java.lang.Thread.State: RUNNABLE
	at java.lang.ref.Reference.waitForReferencePendingList(java.base@22.0.2/Native Method)
	at java.lang.ref.Reference.processPendingReferences(java.base@22.0.2/Reference.java:246)
	at java.lang.ref.Reference$ReferenceHandler.run(java.base@22.0.2/Reference.java:208)

"Finalizer" #10 [30928] daemon prio=8 os_prio=0 cpu=0,07ms elapsed=28,22s tid=0x00007fef88177b60 nid=30928 in Object.wait()  [0x00007fef68d55000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait0(java.base@22.0.2/Native Method)
	- waiting on <0x0000000718201750> (a java.lang.ref.NativeReferenceQueue$Lock)
	at java.lang.Object.wait(java.base@22.0.2/Object.java:375)
	at java.lang.Object.wait(java.base@22.0.2/Object.java:348)
	at java.lang.ref.NativeReferenceQueue.await(java.base@22.0.2/NativeReferenceQueue.java:48)
	at java.lang.ref.ReferenceQueue.remove0(java.base@22.0.2/ReferenceQueue.java:158)
	at java.lang.ref.NativeReferenceQueue.remove(java.base@22.0.2/NativeReferenceQueue.java:89)
	- locked <0x0000000718201750> (a java.lang.ref.NativeReferenceQueue$Lock)
	at java.lang.ref.Finalizer$FinalizerThread.run(java.base@22.0.2/Finalizer.java:173)

"Signal Dispatcher" #11 [30929] daemon prio=9 os_prio=0 cpu=0,21ms elapsed=28,22s tid=0x00007fef88179640 nid=30929 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Service Thread" #12 [30930] daemon prio=9 os_prio=0 cpu=0,12ms elapsed=28,22s tid=0x00007fef8817ac80 nid=30930 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Deflation Thread" #13 [30931] daemon prio=9 os_prio=0 cpu=4,62ms elapsed=28,21s tid=0x00007fef8817c220 nid=30931 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" #14 [30933] daemon prio=9 os_prio=0 cpu=44,56ms elapsed=28,21s tid=0x00007fef8817dd40 nid=30933 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
   No compile task

"C1 CompilerThread0" #17 [30934] daemon prio=9 os_prio=0 cpu=69,87ms elapsed=28,21s tid=0x00007fef88187440 nid=30934 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
   No compile task

"Common-Cleaner" #18 [30940] daemon prio=8 os_prio=0 cpu=0,21ms elapsed=28,20s tid=0x00007fef881a16e0 nid=30940 waiting on condition  [0x00007fef6867f000]
   java.lang.Thread.State: TIMED_WAITING (parking)
	at jdk.internal.misc.Unsafe.park(java.base@22.0.2/Native Method)
	- parking to wait for  <0x00000007182544b0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
	at java.util.concurrent.locks.LockSupport.parkNanos(java.base@22.0.2/LockSupport.java:269)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@22.0.2/AbstractQueuedSynchronizer.java:1847)
	at java.lang.ref.ReferenceQueue.await(java.base@22.0.2/ReferenceQueue.java:71)
	at java.lang.ref.ReferenceQueue.remove0(java.base@22.0.2/ReferenceQueue.java:143)
	at java.lang.ref.ReferenceQueue.remove(java.base@22.0.2/ReferenceQueue.java:218)
	at jdk.internal.ref.CleanerImpl.run(java.base@22.0.2/CleanerImpl.java:140)
	at java.lang.Thread.runWith(java.base@22.0.2/Thread.java:1583)
	at java.lang.Thread.run(java.base@22.0.2/Thread.java:1570)
	at jdk.internal.misc.InnocuousThread.run(java.base@22.0.2/InnocuousThread.java:186)

"Monitor Ctrl-Break" #19 [30952] daemon prio=5 os_prio=0 cpu=5,84ms elapsed=28,14s tid=0x00007fef886c01b0 nid=30952 runnable  [0x00007fef6837c000]
   java.lang.Thread.State: RUNNABLE
	at sun.nio.ch.SocketDispatcher.read0(java.base@22.0.2/Native Method)
	at sun.nio.ch.SocketDispatcher.read(java.base@22.0.2/SocketDispatcher.java:47)
	at sun.nio.ch.NioSocketImpl.tryRead(java.base@22.0.2/NioSocketImpl.java:256)
	at sun.nio.ch.NioSocketImpl.implRead(java.base@22.0.2/NioSocketImpl.java:307)
	at sun.nio.ch.NioSocketImpl.read(java.base@22.0.2/NioSocketImpl.java:346)
	at sun.nio.ch.NioSocketImpl$1.read(java.base@22.0.2/NioSocketImpl.java:796)
	at java.net.Socket$SocketInputStream.implRead(java.base@22.0.2/Socket.java:1108)
	at java.net.Socket$SocketInputStream.read(java.base@22.0.2/Socket.java:1095)
	at sun.nio.cs.StreamDecoder.readBytes(java.base@22.0.2/StreamDecoder.java:350)
	at sun.nio.cs.StreamDecoder.implRead(java.base@22.0.2/StreamDecoder.java:393)
	at sun.nio.cs.StreamDecoder.lockedRead(java.base@22.0.2/StreamDecoder.java:217)
	at sun.nio.cs.StreamDecoder.read(java.base@22.0.2/StreamDecoder.java:171)
	at java.io.InputStreamReader.read(java.base@22.0.2/InputStreamReader.java:186)
	at java.io.BufferedReader.fill(java.base@22.0.2/BufferedReader.java:160)
	at java.io.BufferedReader.implReadLine(java.base@22.0.2/BufferedReader.java:370)
	at java.io.BufferedReader.readLine(java.base@22.0.2/BufferedReader.java:347)
	at java.io.BufferedReader.readLine(java.base@22.0.2/BufferedReader.java:436)
	at com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:49)

"Notification Thread" #20 [30953] daemon prio=9 os_prio=0 cpu=0,06ms elapsed=28,14s tid=0x00007fef886c0b20 nid=30953 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"pool-1-thread-1" #22 [30955] prio=5 os_prio=0 cpu=33,46ms elapsed=28,13s tid=0x00007fef886cf9e0 nid=30955 waiting for monitor entry  [0x00007fef6817a000]
   java.lang.Thread.State: BLOCKED (on object monitor)
	at com.example.demo.HundredMistakes.lambda$main$0(HundredMistakes.java:29)
	- waiting to lock <0x0000000717b21f30> (a java.lang.Object)
	- locked <0x0000000717b21f20> (a java.lang.Object)
	at com.example.demo.HundredMistakes$$Lambda/0x00007fef13003200.run(Unknown Source)
	at java.util.concurrent.Executors$RunnableAdapter.call(java.base@22.0.2/Executors.java:572)
	at java.util.concurrent.FutureTask.run(java.base@22.0.2/FutureTask.java:317)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@22.0.2/ThreadPoolExecutor.java:1144)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@22.0.2/ThreadPoolExecutor.java:642)
	at java.lang.Thread.runWith(java.base@22.0.2/Thread.java:1583)
	at java.lang.Thread.run(java.base@22.0.2/Thread.java:1570)

"pool-1-thread-2" #23 [30956] prio=5 os_prio=0 cpu=1,90ms elapsed=28,13s tid=0x00007fef886d0b20 nid=30956 waiting for monitor entry  [0x00007fef53ffe000]
   java.lang.Thread.State: BLOCKED (on object monitor)
	at com.example.demo.HundredMistakes.lambda$main$1(HundredMistakes.java:46)
	- waiting to lock <0x0000000717b21f20> (a java.lang.Object)
	- locked <0x0000000717b21f30> (a java.lang.Object)
	at com.example.demo.HundredMistakes$$Lambda/0x00007fef13003420.run(Unknown Source)
	at java.util.concurrent.Executors$RunnableAdapter.call(java.base@22.0.2/Executors.java:572)
	at java.util.concurrent.FutureTask.run(java.base@22.0.2/FutureTask.java:317)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@22.0.2/ThreadPoolExecutor.java:1144)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@22.0.2/ThreadPoolExecutor.java:642)
	at java.lang.Thread.runWith(java.base@22.0.2/Thread.java:1583)
	at java.lang.Thread.run(java.base@22.0.2/Thread.java:1570)

"DestroyJavaVM" #24 [30919] prio=5 os_prio=0 cpu=87,66ms elapsed=28,13s tid=0x00007fef88030650 nid=30919 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Attach Listener" #25 [30976] daemon prio=9 os_prio=0 cpu=9,54ms elapsed=27,18s tid=0x00007feefc000e70 nid=30976 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"VM Thread" os_prio=0 cpu=1,71ms elapsed=28,22s tid=0x00007fef88168da0 nid=30926 runnable  

"VM Periodic Task Thread" os_prio=0 cpu=25,07ms elapsed=28,22s tid=0x00007fef8814ef70 nid=30925 waiting on condition  

"G1 Service" os_prio=0 cpu=1,79ms elapsed=28,23s tid=0x00007fef8813d7f0 nid=30924 runnable  

"G1 Refine#0" os_prio=0 cpu=0,06ms elapsed=28,23s tid=0x00007fef8813c830 nid=30923 runnable  

"G1 Conc#0" os_prio=0 cpu=0,05ms elapsed=28,23s tid=0x00007fef880a9560 nid=30922 runnable  

"G1 Main Marker" os_prio=0 cpu=0,08ms elapsed=28,23s tid=0x00007fef880a85c0 nid=30921 runnable  

"GC Thread#0" os_prio=0 cpu=0,07ms elapsed=28,23s tid=0x00007fef88097860 nid=30920 runnable  

JNI global refs: 15, weak refs: 0

Found one Java-level deadlock:
=============================
"pool-1-thread-1":
  waiting to lock monitor 0x00007feed4054a70 (object 0x0000000717b21f30, a java.lang.Object),
  which is held by "pool-1-thread-2"

"pool-1-thread-2":
  waiting to lock monitor 0x00007feec80047b0 (object 0x0000000717b21f20, a java.lang.Object),
  which is held by "pool-1-thread-1"

Java stack information for the threads listed above:
===================================================
"pool-1-thread-1":
	at com.example.demo.HundredMistakes.lambda$main$0(HundredMistakes.java:29)
	- waiting to lock <0x0000000717b21f30> (a java.lang.Object)
	- locked <0x0000000717b21f20> (a java.lang.Object)
	at com.example.demo.HundredMistakes$$Lambda/0x00007fef13003200.run(Unknown Source)
	at java.util.concurrent.Executors$RunnableAdapter.call(java.base@22.0.2/Executors.java:572)
	at java.util.concurrent.FutureTask.run(java.base@22.0.2/FutureTask.java:317)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@22.0.2/ThreadPoolExecutor.java:1144)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@22.0.2/ThreadPoolExecutor.java:642)
	at java.lang.Thread.runWith(java.base@22.0.2/Thread.java:1583)
	at java.lang.Thread.run(java.base@22.0.2/Thread.java:1570)
"pool-1-thread-2":
	at com.example.demo.HundredMistakes.lambda$main$1(HundredMistakes.java:46)
	- waiting to lock <0x0000000717b21f20> (a java.lang.Object)
	- locked <0x0000000717b21f30> (a java.lang.Object)
	at com.example.demo.HundredMistakes$$Lambda/0x00007fef13003420.run(Unknown Source)
	at java.util.concurrent.Executors$RunnableAdapter.call(java.base@22.0.2/Executors.java:572)
	at java.util.concurrent.FutureTask.run(java.base@22.0.2/FutureTask.java:317)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(java.base@22.0.2/ThreadPoolExecutor.java:1144)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(java.base@22.0.2/ThreadPoolExecutor.java:642)
	at java.lang.Thread.runWith(java.base@22.0.2/Thread.java:1583)
	at java.lang.Thread.run(java.base@22.0.2/Thread.java:1570)

Found 1 deadlock.

```

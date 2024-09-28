
[Java 21 overview options](https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html#overview-of-java-options)

[chriswhocodes.com](https://chriswhocodes.com/vm-options-explorer.html) : JITWatch, VM Options Explorer, JaCoLine and other utilities.

[Foojay.io](https://foojay.io/command-line-arguments)

[JVM Options Cheat Sheet (2021)](https://www.jrebel.com/blog/jvm-options-cheat-sheet)

## `-D` (standard)

### javax.net.ssl.trustStore

SSL/TLS: Use native trustore

**Windows** \
Useful in corporate environments \
https://stackoverflow.com/questions/684081/importing-ssl-certificate-into-eclipse \
https://stackoverflow.com/questions/41257366/import-windows-certificates-to-java/49011158#49011158 
```
javax.net.ssl.trustStore=NUL
javax.net.ssl.trustStoreType=Windows-ROOT
```

**Linux** \
https://stackoverflow.com/questions/76131942/how-to-make-the-java-runtime-use-os-ca-certchain \
https://stackoverflow.com/questions/46845251/java-trust-unix-certificate-store

## `-X` (non standard)

### `-Xlog` (Java 9+)

Introduced in Java 9, see [JEP 158: **Unified JVM Logging**](https://openjdk.org/jeps/158) and [JEP 271: **Unified GC Logging**](https://openjdk.org/jeps/271).

Before Java 9, multiple non unified options :
- Garbage collector (GC) event timestamp: -XX:+PrintGCTimeStamps or -XX:+PrintGCDateStamps
- GC event details: -XX:+PrintGCDetails
- GC event cause: -XX:+PrintGCCause
- GC adaptiveness: -XX:+PrintAdaptiveSizePolicy
- GC-specific options (e.g., for the Garbage First [G1] GC): -XX:+G1PrintHeapRegions
- Compilation levels: -XX:+PrintCompilation
- Inlining information: -XX:+PrintInlining
- Assembly code: -XX:+PrintAssembly (requires -XX:+UnlockDiagnosticVMOptions)
- Class histogram: -XX:+PrintClassHistogram
- Information on java.util.concurrent locks: -XX:+PrintConcurrentLocks

By defauly, errors and warnings are logged to stderr (equivalent to `-Xlog:all=warning:stderr:uptime,level,tags`).

```sh

# available tags, log decorators, levels
# examples
java -Xlog:help

# tags
java -Xlog:gc* ...
java -Xlog:thread* ...

# levels (off, error, warning, info, debug, trace)
java -Xlog:monitorinflation*=trace ...

# decorators
java -Xlog:gc*::uptimemillis,pid,tid ...

# file output
java -Xlog:gc*=info:file=gclog.txt ...
java -Xlog:monitorinflation*=trace:file=lockinflation.txt -Xlog:gc*=info:file=gclog.txt ...

# dynamic logging (since Java 11)
jcmd <pid> VM.log what=gc*=trace decorators=uptimemillis,tid,hostname
# current config and available options 
jcmd <pid> help VM.log 

# Enable async logging (since Java 17)
java -Xlog:async -Xlog:gc=debug:file=gc.log -Xlog:safepoint=trace ...

# Show GC and TLAB logs
java -Xlog:gc+tlab=trace

```

### `-Xmn`

Sets the initial and maximum size (in bytes) of the heap for the young generation (nursery) in the generational collectors.

Instead of the -Xmn option to set both the initial and maximum size of the heap for the young generation, you can use `-XX:NewSize` to set the initial size and `-XX:MaxNewSize` to set the maximum size.

```
-Xmn256m
-Xmn262144k
-Xmn268435456
```

### `-Xms`

Sets the minimum and the initial size (in bytes) of the heap. Equivalent to `-XX:MaxHeapSize`.

```
-Xmn256m
-Xmn262144k
-Xmn268435456
```

### `-Xmx`

Specifies the maximum size (in bytes) of the heap.
```
-Xmx83886080
-Xmx81920k
-Xmx80m
```

### `-Xss`

Sets the thread stack size (in bytes). Similar to `-XX:ThreadStackSize`.

```
-Xss1m
-Xss1024k
-Xss1048576
```

## `-XX:` (experimental)

### `-XX:NativeMemoryTracking`
`-XX:NativeMemoryTracking=[off | summary | detail]`

For real-time insights, jcmd can be used to grab the runtime `summary` or `detail` : `jcmd <pid> VM.native_memory summary`


### `–XX:+PrintFlagsFinal`
To check which options are enabled by default.

### `–XX:+UseCompressedOops`
This option has been enabled by default since Java 6, update 23. 



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

### `-XX:MetaspaceSize`


### `-XX:NativeMemoryTracking`

To enable NMT, add `-XX:NativeMemoryTracking=[off | summary | detail]`. \
For real-time insights, use `jcmd <pid> VM.native_memory [summary | detail]`

<details open>

<summary>Example output of jcmd <PID> VM.native_memory</summary>

```sh
$ jcmd 19634 VM.native_memory summary
19634:

Native Memory Tracking:

(Omitting categories weighting less than 1KB)

Total: reserved=5589178KB, committed=337926KB
       malloc: 19878KB #14471
       mmap:   reserved=5569300KB, committed=318048KB

-                 Java Heap (reserved=4050944KB, committed=256000KB)
                            (mmap: reserved=4050944KB, committed=256000KB, at peak) 
 
-                     Class (reserved=1048703KB, committed=319KB)
                            (classes #1327)
                            (  instance classes #1168, array classes #159)
                            (malloc=127KB #2944) (peak=127KB #2946) 
                            (mmap: reserved=1048576KB, committed=192KB, at peak) 
                            (  Metadata:   )
                            (    reserved=65536KB, committed=1344KB)
                            (    used=1288KB)
                            (    waste=56KB =4,18%)
                            (  Class space:)
                            (    reserved=1048576KB, committed=192KB)
                            (    used=120KB)
                            (    waste=72KB =37,55%)
 
-                    Thread (reserved=27813KB, committed=1957KB)
                            (threads #27)
                            (stack: reserved=27728KB, committed=1872KB, peak=1980KB)
                            (malloc=54KB #167) (peak=65KB #177) 
                            (arena=31KB #53) (peak=116KB #35)
 
-                      Code (reserved=247785KB, committed=7645KB)
                            (malloc=97KB #1646) (at peak) 
                            (mmap: reserved=247688KB, committed=7548KB, at peak) 
                            (arena=0KB #0) (peak=1KB #1)
 
-                        GC (reserved=129693KB, committed=55573KB)
                            (malloc=17293KB #1038) (peak=17293KB #1033) 
                            (mmap: reserved=112400KB, committed=38280KB, at peak) 
 
-                 GCCardSet (reserved=29KB, committed=29KB)
                            (malloc=29KB #378) (at peak) 
 
-                  Compiler (reserved=183KB, committed=183KB)
                            (malloc=20KB #57) (peak=20KB #70) 
                            (arena=164KB #4) (peak=1950KB #12)
 
-                  Internal (reserved=282KB, committed=282KB)
                            (malloc=246KB #2966) (peak=255KB #2971) 
                            (mmap: reserved=36KB, committed=36KB, at peak) 
 
-                     Other (reserved=10KB, committed=10KB)
                            (malloc=10KB #2) (at peak) 
 
-                    Symbol (reserved=1213KB, committed=1213KB)
                            (malloc=853KB #1420) (at peak) 
                            (arena=360KB #1) (at peak)
 
-    Native Memory Tracking (reserved=235KB, committed=235KB)
                            (malloc=9KB #135) (peak=9KB #138) 
                            (tracking overhead=226KB)
 
-        Shared class space (reserved=16384KB, committed=12768KB, readonly=0KB)
                            (mmap: reserved=16384KB, committed=12768KB, peak=13016KB) 
 
-               Arena Chunk (reserved=1KB, committed=1KB)
                            (malloc=1KB #62) (peak=2363KB #101) 
 
-                    Module (reserved=170KB, committed=170KB)
                            (malloc=170KB #2069) (at peak) 
 
-                 Safepoint (reserved=8KB, committed=8KB)
                            (mmap: reserved=8KB, committed=8KB, at peak) 
 
-           Synchronization (reserved=157KB, committed=157KB)
                            (malloc=157KB #1532) (peak=157KB #1533) 
 
-            Serviceability (reserved=17KB, committed=17KB)
                            (malloc=17KB #16) (peak=17KB #18) 
 
-                 Metaspace (reserved=65549KB, committed=1357KB)
                            (malloc=13KB #13) (at peak) 
                            (mmap: reserved=65536KB, committed=1344KB, at peak) 
 
-      String Deduplication (reserved=1KB, committed=1KB)
                            (malloc=1KB #8) (at peak) 
 
-                   Unknown (reserved=0KB, committed=0KB)
                            (mmap: reserved=0KB, committed=0KB, peak=20KB)
```
</details>

See [java-jvm-performance.md > Native Memory paragraph](java-jvm-performance.md#native-memory)


### `–XX:+PrintFlagsFinal`
To check which options are enabled by default.

### `–XX:+UseCompressedOops`
This option has been enabled by default since Java 6, update 23. 

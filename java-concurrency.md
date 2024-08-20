## Deadlocks

### Example

```java
final Object lock1 = new Object();

final Object lock2 = new Object();

Runnable task1 = () -> {
    synchronized (lock1) {
	log("Thread 1: locked resource 1");

	try {
	    Thread.sleep(100);
	} catch (InterruptedException e) {
	    log(e.getMessage());
	    Thread.currentThread().interrupt();
	}

	synchronized (lock2) {
	    log("Thread 1: locked resource 2");
	}
    }
};

Runnable task2 = () -> {
    synchronized (lock2) {
	log("Thread 2: locked resource 2");

	try {
	    Thread.sleep(100);
	} catch (InterruptedException e) {
	    log(e.getMessage());
	    Thread.currentThread().interrupt();
	}

	synchronized (lock1) {
	    log("Thread 2: locked resource 1");
	}
    }
};

ExecutorService executorService = Executors.newFixedThreadPool(5);
executorService.submit(task1);
executorService.submit(task2);
```

### Detect deadlocks outside the JVM

With `jconsole <pid>`, there's a **Detect deadlocks** button.

With `jstack <pid>` or `jcmd <pid> Thread.print` to output a thread dump.

### Detect deadlocks from within the code

Spawn a periodic thread to run `ThreadMXBean.findDeadlockedThreads()`
```java
Runnable detectDeadlocksTask = () -> {
    ThreadMXBean tmx = ManagementFactory.getThreadMXBean();
    long[] ids = tmx.findDeadlockedThreads();
    if (ids != null) {
        ThreadInfo[] infos = tmx.getThreadInfo(ids, true, true);
        System.out.println("The following threads are deadlocked:");
        for (ThreadInfo ti : infos) {
            System.out.println(ti);
        }
    }
};

ScheduledExecutorService cronExecutorService = Executors.newScheduledThreadPool(2);
cronExecutorService.scheduleAtFixedRate(detectDeadlocksTask, 5, 5, TimeUnit.SECONDS);
```

Output :
```
The following threads are deadlocked:
"pool-1-thread-1" prio=5 Id=22 BLOCKED on java.lang.Object@126d0622 owned by "pool-1-thread-2" Id=23
	at app//com.example.demo.HundredMistakes.lambda$main$0(HundredMistakes.java:34)
	-  blocked on java.lang.Object@126d0622
	-  locked java.lang.Object@557ba567
	at app//com.example.demo.HundredMistakes$$Lambda/0x00007f5c97003200.run(Unknown Source)
	at java.base@22.0.2/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:572)
	at java.base@22.0.2/java.util.concurrent.FutureTask.run(FutureTask.java:317)
	at java.base@22.0.2/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base@22.0.2/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base@22.0.2/java.lang.Thread.runWith(Thread.java:1583)
	at java.base@22.0.2/java.lang.Thread.run(Thread.java:1570)

	Number of locked synchronizers = 1
	- java.util.concurrent.ThreadPoolExecutor$Worker@7637f22


"pool-1-thread-2" prio=5 Id=23 BLOCKED on java.lang.Object@557ba567 owned by "pool-1-thread-1" Id=22
	at app//com.example.demo.HundredMistakes.lambda$main$1(HundredMistakes.java:51)
	-  blocked on java.lang.Object@557ba567
	-  locked java.lang.Object@126d0622
	at app//com.example.demo.HundredMistakes$$Lambda/0x00007f5c97003420.run(Unknown Source)
	at java.base@22.0.2/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:572)
	at java.base@22.0.2/java.util.concurrent.FutureTask.run(FutureTask.java:317)
	at java.base@22.0.2/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base@22.0.2/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base@22.0.2/java.lang.Thread.runWith(Thread.java:1583)
	at java.base@22.0.2/java.lang.Thread.run(Thread.java:1570)

	Number of locked synchronizers = 1
	- java.util.concurrent.ThreadPoolExecutor$Worker@4926097b
```

Remark : There's another method `findMonitorDeadlockedThreads()` that only detects object monitors (synchronized methods or blocks). `findDeadlockedThreads()` detects more types of monitors inside the JVM. 

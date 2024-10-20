## CountDownLatch

Permet à des threads d'attendre d'autres threads via un compteur. Plus versatile à utiliser qu'un `CyclicBarrier` mais contrairement à lui n'est pas réutilisable.

Mode d'emploi :
- Instancier la classe `CountDownLatch` avec `n` = le compte de départ.
- Appeler `countDownLatch.countDown()` pour décrémenter le compteur.
- Appeler `countDownLatch.await()` pour être bloqué.

Pour être débloqué, il faut que le compteur atteigne zéro.

Les exemples suivants sont issus de la [javadoc](https://docs.oracle.com/en/java/javase/23/docs/api/java.base/java/util/concurrent/CountDownLatch.html).

Exemple 1 : 
1. Un CountDownLatch(1) est créé.
2. Plusieurs threads attendent en appelant `countDownLatch.await()`.
3. Un thread appelle `countDownLatch.countDown()` pour les libérer.

Exemple 2 : 
1. Un CountDownLatch(N) est créé.
2. N threads appelent `countDownLatch.countDown()` quand ils ont fini.
3. Un thread appelle `countDownLatch.await()` pour être débloqué quand tous les threads ont terminé.

## CyclicBarrier

Permet à des threads d'attendre que tous soit arrivés à un point pour continuer (barrière). La barrière est réutilisable (cyclique) contrairement à la classe [CountDownLatch](https://docs.oracle.com/en/java/javase/23/docs/api/java.base/java/util/concurrent/CountDownLatch.html).

Mode d'emploi :
- Instancier la classe `CyclicBarrier` avec `n` = nombre de threads.
- Passer l'instance de `CyclicBarrier` à chaque thread qui appelle `cyclicBarrier.await()` quand il arrive au point de synchronisation et veut être bloqué.

Pour être débloqués, tous les threads doivent appeler `cyclicBarrier.await()`. 

Exemple :
```java

var service = Executors.newFixedThreadPool(4);
try {
  int n = 5;
  var c1 = new CyclicBarrier(n);
  var c2 = new CyclicBarrier(n, () -> System.out.println("*** Terminé !"));

  for (int i = 0; i < n; i++)
    service.submit(() -­> performTask(c1, c2));
  }
finally {
  service.shutdown();
}

...

void performTasks(CyclicBarrier c1, CyclicBarrier c2) {
  try {
    task1();
    c1.await();
    task2();
    c2.await();
  } catch (InterruptedException | BrokenBarrierException e) {
    // gestion des exceptions
  }
}
```

<details>
  <summary>Sortie console</summary>

```
task1
task1
task1
task1
task1
task2
task2
task2
task2
task2
*** Terminé !
```

</details>

## Deadlocks


<details>
  <summary>Code reproduisant un deadlock</summary>

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
</details>


### Detection outside the JVM

With `jconsole <pid>`, there's a **Detect deadlocks** button.

With `jstack <pid>` or `jcmd <pid> Thread.print` to output a thread dump.

### Detection from within the code

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

<details>
  <summary>Output</summary>

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
</details>

**NB** : There's another method `findMonitorDeadlockedThreads()` that only detects object monitors (synchronized methods or blocks). `findDeadlockedThreads()` detects more types of monitors inside the JVM. 

## Livelock

Cas spécifique de [starvation](#starvation).

Deux threads sont dans une situation de deadlock. Ils décident de relâcher leurs ressources en même temps et vont vérouiller l'autre ressource en même temps aussi. Ils se retrouvent dans une nouvelle situation de deadlock et le scénario se rejoue à l'infini.   

## Lock framework

- Interface `Lock`
- Plusieurs implémentations : `ReentrantLock`, `ReentrantReadWriteLock`, etc...
- Demande de verrou non bloquante, contrairement à `synchronized`
- Chaque `lock()` doit être relâché par un `unlock()`, possibilité d'imbriquer un même verrou
- Le constructeur de `ReentrantLock` supporte un booleen `fair` (par défaut, false) pour l'octroi du verrou dans l'ordre de demande des threads. Risque d'impact des performances.
- Appeler `unlock()` sur un verrou locké par un autre déclenche un `IllegalMonitorStateException`

Méthodes
```java
void lock ()
void unlock ()
boolean tryLock ()
boolean tryLock (long timeout, TimeUnit unit)
```

Usage

<details>
  <summary>lock()</summary>

```java
Lock lock = new ReentrantLock();
try {
  lock.lock();
  ...
} finally {
  lock.unlock();
}
```
</details>


<details>
  <summary>tryLock()</summary>

```java
Lock lock = new ReentrantLock();
if(lock.tryLock()) {
  try {
    System.out.println("Verrou obtenu, début section critique...");
  } finally {
    lock.unlock();
  }
} else {
  System.out.println("Verrou non obtenu, on fait autre chose...");
}
```
</details>

<details>
  <summary>tryLock(long, TimeUnit)</summary>

```java
Lock lock = new ReentrantLock();
if(lock.tryLock(10, TimeUnit.SECONDS)) {
  try {
    System.out.println("Verrou obtenu, début section critique...");
  } finally {
    lock.unlock();
  }
} else {
  System.out.println("Verrou non obtenu, on fait autre chose...");
}
```
</details>

`ReentrantReadWriteLock` implémente une paire de verrous, un optimisé pour les accès en lecture et un autre pour les accès en écriture. Convient aux applications où les accès en lectures sont plus nombreux qu'en écriture. 

## Starvation

Terme (anglais) sigifiant qu'un thread n'arrive jamais à accéder à une ressource synchronisée.

## Run Jenkins
```sh
export JENKINS_HOME=...
export JAVA_HOME=...
$JAVA_HOME/bin/java -jar jenkins-2.332.3-LTS.war --httpPort=8980 --prefix=... &> jenkins.log &
```
## Groovy console scripts

Recharger un job depuis le disque
```java
import jenkins.model.Jenkins;

Jenkins j = Jenkins.get()
def job = j.getItemByFullName("folder1/folder2/job_name")
job.doReload()
```

Vider la file des jobs
```java
// tout
Jenkins.instance.queue.clear()

// vidage ciblé
import hudson.model.*
def q = Jenkins.instance.queue
q.items.findAll { it.task.name.startsWith('REPLACEME') }.each { q.cancel(it.task) }
```

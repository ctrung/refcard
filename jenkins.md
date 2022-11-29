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

## Notifications mail (pipeline)

Déclarative 
```
    pipeline {
        ...
        post {
            failure {
                script { ... }
            }
            fixed {
                script { ... }
            }
        }
    }
```


Script
```
// ajout implicite de l'admin aux notifications
def to = (notifyMailTo != null ? "$notifyMailTo," : '') + 'admin@xxxx.xxx'
println("Envoi notification à : $to")

// basic mail
mail to: to,
  body: "See $BUILD_URL",
  charset: 'UTF-8',
  mimeType: 'text/html',
  subject: "Jenkins job failed : $JOB_NAME #$BUILD_NUMBER"

// extended email plugin
// jelly template
emailext body: '${JELLY_SCRIPT,template="html"}',
  // attachLog: true,
  // compressLog: true,
  recipientProviders: [culprits()],
  subject: "Jenkins job failed : $JOB_NAME #$BUILD_NUMBER", 
  to: to

// extended email plugin
// groovy script template + trigger with postsendScript attribute
emailext body: '${SCRIPT, template="groovy-html.template"}',
  recipientProviders: [culprits()],
  subject: "Job ${currentBuild.currentResult} in Jenkins : $JOB_NAME #$BUILD_NUMBER",
  to: to,
  // send email if the current build is not SUCCESS or it is SUCCESS though the previous build result was worse
  postsendScript: '!build.result.toString().equals(\'SUCCESS\') || build.previousBuild.resultIsWorseOrEqualTo(\'UNSTABLE\')'
```

Global variables reference in pipeline \
${YOUR_JENKINS_URL}/pipeline-syntax/globals

Extended email plugin \
https://plugins.jenkins.io/email-ext/ \
https://github.com/jenkinsci/email-ext-plugin/tree/master/src/main/resources/hudson/plugins/emailext/templates

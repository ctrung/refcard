## Access admin

Accès admin de secours.

https://www.jenkins.io/doc/book/security/access-control/disable/



## Envinject plugin

https://plugins.jenkins.io/envinject/

Can also manage passwords globally, see \
https://stackoverflow.com/questions/27152656/jenkins-access-global-passwords-in-powershell \
for Envinject plugin vs Credentials Binding plugin for differences.

## Console groovy script

Accès environnement
```java
// system
build.getEnvironment(listener).get('XXX')
// normal
System.getEnv('XXX')
```

Résultat du job
```java
// https://javadoc.jenkins-ci.org/hudson/model/Result.html
build.result=hudson.model.Result.XXX   // FAILURE, UNSTABLE, SUCCESS, etc...
```

## Git 

https://docs.cloudbees.com/docs/cloudbees-ci-kb/latest/client-and-managed-controllers/jenkins-with-git-client-needs-permission-on-temporary-directory

**Possible issues**

*Description* \
On pipelines + shared libraries, git client + askpass script + **/tmp** fails when git querying the shared lib infos (`git ls-remote -h <GIT_SHARED_LIB_URL>`) although previous git querying on project's repo works. 

*Solution* \
Setting tmpdir location to a local folder : `java -Djava.io.tmpdir=... -jar jenkins.jar`  

## Redirect unauthenticated users to login page instead of 404

https://groups.google.com/g/jenkinsci-users/c/Knbk-ouwutk

It's a permission misconfiguration problem : best practice is anonymous role + anonymous permission without anything. 

## Run Jenkins
```sh
#!/bin/sh

export JENKINS_HOME=...
export JAVA_HOME=...
export PATH=$JAVA_HOME/bin:$PATH

java -jar ./jenkins.war --prefix=/xxx --httpPort=... > jenkins.log 2>&1 &
```

## Pipelines

### Changer la description du job
Vu sur [Stackoverflow](https://stackoverflow.com/a/49042225) 

`currentBuild.rawBuild.project.description = 'NEW JOB DESCRIPTION'`

### Notifications mail

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

### Template for pipeline job
(declarative)
```
@Library('libname-configured-in-jenkins@git-refname') _

pipeline {
    agent {
        label 'ci'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '4'))
        timeout(time: 1, unit: 'HOURS')
    }

    tools {
        jdk     "XXX"
        nodejs  "XXX"
        gradle  "XXX"
        maven   "XXX"
    }
    
    environment {
        VAR_1           = 'XXX'
        VAR_2           = 'XXX'
        ...
    }

    parameters {
        booleanParam(name: 'With fun ?',      description: 'My desc',        defaultValue: false)
        string(name: 'Name',       description: 'My desc')
    }

    stages {

        // Manual git clone : useful if pipeline is defined inside job definition (pipeline script option)
        stage('Git Checkout') {
            steps {
                cleanWs()
                git branch: 'XXX', credentialsId: 'XXX', url: "XXX"
            }
        }

        stage('First step') {
            when {
                expression { env[VAR_1] != 'true' }
                expression { !(env.VAR_2.endsWith('suffix')) }
            }
            steps {
                def gitUrl = scm.userRemoteConfigs[0].url
                ...
            }
        }
    }
}
```

(scripted)
```
@Library('libname-configured-in-jenkins@git-refname') _
import hudson.model.*

properties(
        [
                parameters([
                        string(name: 'XXX', description: 'My desc'),
                        booleanParam(defaultValue: false, name: 'XXX', description: 'My desc')
                ])
        ]
)

node {
    def gitInfos = [:]

    // examples of things done with scripted pipelines

    stage('XXX') {
        sh "rm -f file1"
        def var = sh (script: "cat file, returnStdout: true).trim() as int // read a number from a file on disk 
        println "log..."
        def var = params.XXX?.split(/\s*,\s*/) // parse to array
    
        env.XXX = true
    
        for (item in Hudson.instance.items) {
            if (item.name.endsWith('XXX')) {
                // NB : Job run through replay button differs in scm repos order (if pipeline libs are activated)
                //      > with replay  : [pipeline lib, project repo]
                //      > no replay :    [project repo, pipeline lib]
                def projectSCM = item.getSCMs().find {!it.userRemoteConfigs[0].url.contains('pipeline-libs-repo')}
            }
        }

        dir (folder) {
            // do something inside this folder
        }

        // git clone
        git branch: 'XXX', credentialsId: 'XXX', url: 'XXX'

        // retrieve java home from configured Java installation in Jenkins
        jdk = tool name: 'Java 17'
        env.JAVA_HOME = "$jdk"

        // do something with a fiex version of node declared in Jenkins 
        nodejs(nodeJSInstallationName: 'nodejsName') {
            ...
        }

        withCredentials( [usernamePassword(credentialsId: 'ID', usernameVariable: 'VAR_1', passwordVariable: 'VAR_2')]) {
            ...
        }

        // call funtion defined outside of pipeline
        function...
    }
}

// function with error handling
def funtion(arg1, arg2) {
    catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE', message: "Echec scan de $projectname") {
        
    }
}
```

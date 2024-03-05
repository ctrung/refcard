## Bidouiller les params d'un job

Inspiré de [Display job parameters](https://wiki.jenkins.io/display/JENKINS/Display+job+parameters)

```java
import hudson.model.*
import org.jvnet.hudson.plugins.repositoryconnector.*

names = ["job1", "job2", "job3", ...]

nameTemplate = "jobT"
jobTemplate = Hudson.instance.getItemByFullName(nameTemplate)
println "jobTemplate : $jobTemplate"

/*
paramsDef = jobTemplate.getProperty(ParametersDefinitionProperty.class)
println "jobTemplate.params : ${paramsDef.parameterDefinitions*.name}"
println "jobTemplate.params.PROJECT_VERSION.artifactId : ${paramsDef.getParameterDefinition('PROJECT_VERSION').artifactId}"
println "jobTemplate.params.PROJECT_ARTIFACT_ID : ${paramsDef.getParameterDefinition('PROJECT_ARTIFACT_ID').defaultParameterValue.value}"
println "jobTemplate.params.PROJECT_BATCH_NAME : ${paramsDef.getParameterDefinition('PROJET_BATCH_NAME').defaultParameterValue.value}"
println "jobTemplate.params.PROJECT_PATH_DEPOT : ${paramsDef.getParameterDefinition('PROJECT_PATH_DEPOT').defaultParameterValue.value}"
*/

if (jobTemplate == null) {
    println "Job template $nameTemplate doesn't exist, END"
} else {
	for(n in names) {
	  println "Creating job $n"
	  def job = Hudson.instance.copy(jobTemplate, n)
	  updateProperties(job, n) // job name == artifactId
	  job.save()
	}
}

def updateProperties(job, artifactId) {
  def params = job.getProperty(ParametersDefinitionProperty.class)
  
  // https://javadoc.jenkins.io/plugin/repository-connector/org/jvnet/hudson/plugins/repositoryconnector/VersionParameterDefinition.html
  def oldP = params.getParameterDefinition('PROJECT_VERSION')
  def newP = new VersionParameterDefinition(oldP.name, oldP.description, oldP.groupId, artifactId)
  newP.includeReleases = oldP.includeReleases
  newP.includeSnapshots = oldP.includeSnapshots
  newP.oldestFirst = oldP.oldestFirst
  newP.useLatest = oldP.useLatest
  newP.useRelease = oldP.useRelease
  params.parameterDefinitions.remove(oldP)
  params.parameterDefinitions.add(0, newP)
  
  // https://javadoc.jenkins.io/plugin/hidden-parameter/com/wangyin/parameter/WHideParameterDefinition.html
  params.getParameterDefinition('PROJECT_ARTIFACT_ID').defaultValue = artifactId
}
```



## Créer un job pipeline
[How I can programmatically modify the scm source of pipelines scripts?](https://stackoverflow.com/questions/60636797/how-i-can-programmatically-modify-the-scm-source-of-pipelines-scripts)

```java
import jenkins.model.Jenkins
import hudson.plugins.git.*

Jenkins j = Jenkins.get()

def n = j.createProject(org.jenkinsci.plugins.workflow.job.WorkflowJob.class, 'pipeline-ctr')

def url = 'https://dev.azure.com/xxx/xxx/_git/xxx'
def credentials = 'token-azure'
def branch = 'develop'

def scm = new GitSCM(GitSCM.createRepoList(url, credentials),
                Collections.singletonList(new BranchSpec(branch)),
                false, Collections.<SubmoduleConfig>emptyList(),
                null, null, Collections.singletonList(new hudson.plugins.git.extensions.impl.LocalBranch(branch)))

n.definition = new org.jenkinsci.plugins.workflow.cps.CpsScmFlowDefinition(scm, "Jenkinsfile")
n.save()
```

## Créer une vue

ListView
```java
import jenkins.model.Jenkins
import hudson.model.ListView

Jenkins jenkins = Jenkins.getInstance()
def viewName = 'MyView'
jenkins.addView(new ListView(viewName))
myView = hudson.model.Hudson.instance.getView(viewName)
myView.doAddJobToView('MyJob1')
myView.doAddJobToView('MyJob2')
myView.doAddJobToView('MyJob3')
jenkins.save()
```

CategorizedView
```java
import jenkins.model.Jenkins
import org.jenkinsci.plugins.categorizedview.CategorizedJobsView
import org.jenkinsci.plugins.categorizedview.GroupingRule

def view = new CategorizedJobsView(trigramme)
view.includeRegex = "job-test-$trigramme.*"
view.categorizationCriteria.add(new GroupingRule('CI.*', '1.Intégration continue', false))
view.categorizationCriteria.add(new GroupingRule('deploy.*', '2. Déploiement sur $1', false))

Jenkins jenkins = Jenkins.getInstance()
jenkins.addView(view)
jenkins.save()
```

## Désactiver les jobs

```java
import hudson.model.*

disableChildren(Hudson.instance.items)

def disableChildren(items) {
  for (item in items) {
    if (item.class.canonicalName == 'com.cloudbees.hudson.plugins.folder.Folder') {
        disableChildren(((com.cloudbees.hudson.plugins.folder.Folder) item).getItems())
    //} else if (item.class.canonicalName != 'org.jenkinsci.plugins.workflow.job.WorkflowJob') {
    } else {
      item.disabled=true
      item.save()
      println(item.name)
    }
  }
}
```

## Recharger un job depuis le disque
```java
import jenkins.model.Jenkins;

Jenkins j = Jenkins.get()
def job = j.getItemByFullName("folder1/folder2/job_name")
job.doReload()
```

## Réinitialiser le "next build number"

[How To Reset Build Number in Jenkins](https://www.youtube.com/watch?v=n2Dmhmp--20)

```java
// Reset du next build number à 42
Jenkins.instance.getItemByFullName('XXX-job').updateNextBuildNumber(42)

// Check du next build number 
Jenkins.instance.getItemByFullName('XXX-job').getNextBuildNumber() 

// Suppression des anciens builds + reset du next build number à 1
def item = Jenkins.instance.getItemByFullName('XXX-job')
item.builds.each { it.delete() }
item.updateNextBuildNumber(1)
```

## Vider la file des jobs
```java
// tout
Jenkins.instance.queue.clear()

// vidage ciblé
import hudson.model.*
def q = Jenkins.instance.queue
q.items.findAll { it.task.name.startsWith('REPLACEME') }.each { q.cancel(it.task) }
```

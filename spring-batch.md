# Liens intéressants

[Revisit the chunk-oriented processing model implementation #3950 (closed)](https://github.com/spring-projects/spring-batch/issues/3950)

[Revisit the mechanism of job registration #4547 (closed)](https://github.com/spring-projects/spring-batch/issues/4547)

[Stackoverflow : Role of PlatformTransactionManager with Spring batch 5](https://stackoverflow.com/questions/75087558/role-of-platformtransactionmanager-with-spring-batch-5)

# Guides de migration

[Spring Batch 5](https://github.com/spring-projects/spring-batch/wiki/Spring-Batch-5.0-Migration-Guide) (fin 2022)

[Spring Batch 6](https://github.com/spring-projects/spring-batch/wiki/Spring-Batch-6.0-Migration-Guide) (fin 2025)

# Arrêt brutal d'un job

En fonction de la situation, il faut potentiellement mettre à jour manuellement le statut du jobExecution à FAILED ou ABANDONED.

FAILED : permettra le redémarrage du job (si supporté)
ABANDONNED : interdit le redémarrage du job

ABANDONNED est aussi supporté pour le stepExecution, auquel cas le job passe au step suivant.

https://docs.spring.io/spring-batch/reference/job/advanced-meta-data.html#aborting-a-job

# `BatchStatus` vs `ExitStatus`

`BatchStatus` : propriété de `JobExecution` et `StepExecution`.

`ExitStatus` : utilisé dans le code pour par ex. personnaliser le workflow du job (au sein des `listener.after()` et dans la définition du job avec la clause `on()`).

Règles : 
- pour les steps, fonction de ce qui a été défini dans le code
- pour le job, fonction de ce qui a été configuré dans la définition, soit en utilisant `end()` = SUCCESS, `fail()` = FAILED ou `stop()` = STOPPED, soit en se basant sur le `ExitStatus` du dernier step. Si `ExitStatus` du dernier step est FAILED, alors le job est FAILED, sinon il est COMPLETED.

https://docs.spring.io/spring-batch/reference/step/controlling-flow.html#batchStatusVsExitStatus

# Initialisation auto db

Pour créer automatiquement les tables de spring batch au démarrage spring boot.

Cf. classe `org.springframework.boot.autoconfigure.batch.BatchDataSourceScriptDatabaseInitialiser` appelée lors de l'autoconfigure de Spring Batch (`org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration`)

Propriété (classe `BatchProperties`) : `spring.batch.jdbc.initialize-schema: always|embedded(défaut)|never`

Il y a aussi `spring.batch.jdbc.schema` qui est utilisé au sein du placeholder du script sql : (défaut) `classpath:org/springframework/batch/core/schema-@@platform@@.sql`

# Personnaliser la configuration spring batch

Annoter la configuration @EnableBatchProcessing \
**ou** \
étendre la configuration avec `DefaultBatchConfiguration`. 

Il n'est pas possible de combiner les deux méthodes.

```java

@Configuration
@EnableBatchProcessing(dataSourceRef = "batchDataSource",
			transactionManagerRef = "batchTransactionManager"),
			tablePrefix = "BATCH_", 
			maxVarCharLength = 2500,
			isolationLevelForCreate = "SERIALIZABLE")
public class MyJobConfiguration {

	@Bean
	public DataSource batchDataSource() {
		return new EmbeddedDatabaseBuilder().setType(EmbeddedDatabaseType.HSQL)
				.addScript("/org/springframework/batch/core/schema-hsqldb.sql")
				.generateUniqueName(true).build();
	}

	@Bean
	public JdbcTransactionManager batchTransactionManager(DataSource dataSource) {
		return new JdbcTransactionManager(dataSource);
	}

	@Bean
	public Job job(JobRepository jobRepository) {
		return new JobBuilder("myJob", jobRepository)
				//define job flow as needed
				.build();
	}

}


@Configuration
class MyJobConfiguration extends DefaultBatchConfiguration {

	...

	@Override
	protected Charset getCharset() {
		return StandardCharsets.ISO_8859_1;
	}
}
```

Il est possible d'éviter les clashs de noms en créant des espaces de noms distincts avec `@EnableBatchProcessing(modular=true)`. Sous le capot, un bean `AutomaticJobRegistrar` s'occupe de créer autant d'`ApplicationContext` enfants que voulus.

```java
@Configuration
@EnableBatchProcessing(modular=true)
public class AppConfig {

	@Bean
	public ApplicationContextFactory someJobs() {
		 return new GenericApplicationContextFactory(SomeJobConfiguration.class);
	}
	
	@Bean
	public ApplicationContextFactory moreJobs() {
		 return new GenericApplicationContextFactory(MoreJobConfiguration.class);
	}
	
	...

}
```


# JobLauncher

Interface pour démarrer un `Job` + `JobParameters`. Retourne un `JobExecution`.

```java
public interface JobLauncher {

	public JobExecution run(Job job, JobParameters jobParameters)
				throws JobExecutionAlreadyRunningException, JobRestartException,
					   JobInstanceAlreadyCompleteException, JobParametersInvalidException;
	}
}
```

`JobInstanceAlreadyCompleteException` : thrown if a job instance has already executed (can't run more than once)
`JobRestartException` : indique qu'un job ne peut pas être redémarré parce que `jobBuilder.preventRestart()` a été configuré.


Personnaliser un job launcher pour qu'il retourne la main immédiatement (asynchrone) 

```java

@Bean
public JobLauncher jobLauncher() {
	TaskExecutorJobLauncher jobLauncher = new TaskExecutorJobLauncher();
	jobLauncher.setJobRepository(jobRepository());
	jobLauncher.setTaskExecutor(new SimpleAsyncTaskExecutor());
	jobLauncher.afterPropertiesSet();
	return jobLauncher;
}

```

# JobInstance et JobExecution

`JobInstance` : uniquely identified by a job name and a set of identifying parameters
`JobExecution` : exécution d'un `JobInstance`. Plusieurs exécutions sont attachées à un `JobInstance` en cas de redémarrage par exemple.

Vérifier le statut d'un job : 

```java
jobExecution.getStatus() == BatchStatus.COMPLETED
jobExecution.getStatus() == BatchStatus.FAILED
jobExecution.getStatus() == BatchStatus.UNKNOWN   // statut si eg. lancement asynchrone
```

# Démarrer un job

## Mode CLI sans Spring Boot : utiliser la classe `CommandLineJobRunner`. 

Idéal lorsque les jobs sont lancés par un orchestrateur externe. 

Syntaxe : `java CommandLineJobRunner <jobPath> <jobName> [...<jobParams>...]`

Exemples 

```sh
# config xml
java CommandLineJobRunner endOfDayJob.xml endOfDay \
                                 schedule.date=2007-05-05,java.time.LocalDate,true \    #  <--- le 3ème paramètre booleen définit si le param est "identifying" ou non
                                 vendor.id=123,java.lang.Long,false

# java config
java CommandLineJobRunner io.spring.EndOfDayJobConfiguration endOfDay \
                                 schedule.date=2007-05-05,java.time.LocalDate,true \
                                 vendor.id=123,java.lang.Long,false
```

## Mode CLI avec Spring Boot : utiliser la classe `JobLauncherApplicationRunner`

Cette classe est un Spring Boot ApplicationRunner qui sait lancer des jobs Spring Batch.

**NB** : `JobLauncherCommandLineRunner` est déprécié (https://github.com/spring-projects/spring-boot/issues/19442).

```
# NB : job arg passé en CLI avec la vieille syntaxe spring batch v4, cf chap plus bas et plus haut pour des exemples avec la nouvelle syntaxe
java -jar demo.jar name=Michael
java -jar demo.jar executionDate(date)=2017/11/28                 # specify type for JobParameter conversion
java -jar demo.jar executionDate(date)=2017/11/28 -name=Michael   # specify non identifying JobParameter
```

## Syntaxe passage des arguments du batch en mode CLI

Le passage des arguments d'un job a changé de syntaxe entre spring batch v4 et v5 en raison de https://github.com/spring-projects/spring-boot/issues/19442 : \
https://github.com/spring-projects/spring-batch/wiki/Spring-Batch-5.0-Migration-Guide#default-job-parameter-conversion

*Avant, en v4* :

`[+|-]parameterName(parameterType)=value`

Exemples 
```
java -jar demo.jar name=Michael
java -jar demo.jar executionDate(date)=2017/11/28                 # specify type for JobParameter conversion
java -jar demo.jar executionDate(date)=2017/11/28 -name=Michael   # specify non identifying JobParameter
```

*Après, à partir de v5* :

`parameterName=parameterValue,parameterType,identificationFlag` (simple) \
`parameterName='{"value": "parameterValue", "type":"parameterType", "identifying": "booleanValue"}'` (étendue, par exemple pour tenir compte de la virgule)



## Depuis un container web, eg. via un appel WS-REST

```java
@Controller
public class JobLauncherController {

    @Autowired
    JobLauncher jobLauncher;   // si le batch est long, il est recommandé d'utiliser un job launcher asynchrone, cf. chapitre plus haut sur le JobLauncher

    @Autowired
    Job job;

    @RequestMapping("/jobLauncher.html")
    public void handle() throws Exception{
        jobLauncher.run(job, new JobParameters());
    }
}
```


## Codes de retour du batch

Codes de retour supportés par défaut : 
- 0 : succès
- 1 : échec
- 2 : erreurs spécifique au job runner, eg. job demandé non trouvé

Implémenter `ExitCodeMapper` pour personnaliser d'autres codes de retour (mapping entre les valeurs de `ExitStatus` et des entiers).

# JobParameters

Wrapper autour de `java.util.Map<String, JobParameter>`, supporte la conversion via des getters typés.

## Usages courants

1. Via `chunkContext.getStepContext().getJobParemeters()` (nécessite un cast car c'est une `Map<String, Object>`)
2. Via du late binding avec `@StepScope`
```java
@Bean
@StepScope
protected Callable<String> value(@Value("#{stepExecution.stepName}") final String value) {
  ...
}

@Bean
@StepScope	
public Tasklet helloWorldTasklet(@Value("#{jobParameters['name']}") String name) {
```

## Validation des JobParameters

https://docs.spring.io/spring-batch/reference/job/configuring.html#jobparametersvalidator

```java
@Bean
public JobParametersValidator validator() { ... }

@Bean
public Job job1(JobRepository jobRepository) {
    return new JobBuilder("job1", jobRepository)
                     .validator(validator())
                     ...
                     .build();
}
```

Solution 1 :
- Implémenter `JobParametersValidator`
- Rejeter une `JobParametersInvalidException` dans la méthode validate()

Solution 2 : \
Utiliser `DefaultJobParametersValidator` de Spring pour définir les paramètres obligatoires et facultatifs.

Il est aussi possible de composer plusieurs validators en un seul avec `CompositeJobParametersValidator`.


## JobParametersIncrementer

Permet d'introduire un paramètre implicite qui s'incrémente à chaque exécution du job. Utile pour générer un nouveau `JobInstance`.

Procédure :
1. tenir compte du paramètre dans le `JobParametersValidator` (optionnel)
2. appeler `jobBuilder.incrementer(JobParametersIncrementer)`

Spring Batch fournit la classe `RunIdIncrementer` qui supporte un argument de type long appelé **run.id**.

Exemple d'implémentation avec un timestamp
```java
public class DailyJobTimestamper implements JobParametersIncrementer {
  @Override
  public JobParameters getNext(JobParameters parameters) {
    return new JobParametersBuilder(parameters)
	  .addDate("currentDate", new Date())
	  .toJobParameters();
  }
}
```

# JobExplorer, JobOperator

`JobExplorer` : (interface) couche d'accès bas niveau pour lire dans les metadata spring batch.

```java
public interface JobExplorer {

    List<JobInstance> getJobInstances(String jobName, int start, int count);

    JobExecution getJobExecution(Long executionId);

    StepExecution getStepExecution(Long jobExecutionId, Long stepExecutionId);

    JobInstance getJobInstance(Long instanceId);

    List<JobExecution> getJobExecutions(JobInstance jobInstance);

    Set<JobExecution> findRunningJobExecutions(String jobName);
}
```

`JobOperator` : (interface) couche d'accès haut niveau pour effectuer des actions sur les jobs. 

```java
public interface JobOperator {

    List<Long> getExecutions(long instanceId) throws NoSuchJobInstanceException;

    List<Long> getJobInstances(String jobName, int start, int count)
          throws NoSuchJobException;

    Set<Long> getRunningExecutions(String jobName) throws NoSuchJobException;

    String getParameters(long executionId) throws NoSuchJobExecutionException;

    Long start(String jobName, String parameters)
          throws NoSuchJobException, JobInstanceAlreadyExistsException;

    Long restart(long executionId)
          throws JobInstanceAlreadyCompleteException, NoSuchJobExecutionException,
                  NoSuchJobException, JobRestartException;

    Long startNextInstance(String jobName)
          throws NoSuchJobException, JobParametersNotFoundException, JobRestartException,
                 JobExecutionAlreadyRunningException, JobInstanceAlreadyCompleteException;

    boolean stop(long executionId)
          throws NoSuchJobExecutionException, JobExecutionNotRunningException;

    String getSummary(long executionId) throws NoSuchJobExecutionException;

    Map<Long, String> getStepExecutionSummaries(long executionId)
          throws NoSuchJobExecutionException;

    Set<String> getJobNames();

}
```

Exemple : arrêter un job proprement

```java
Set<Long> executions = jobOperator.getRunningExecutions("sampleJob");
jobOperator.stop(executions.iterator().next());
```

https://docs.spring.io/spring-batch/reference/job/advanced-meta-data.html

# JobRegistry

Permet de trouver les jobs au sein du contexte Spring, notamment si ceux ci sont répartis dans des contextes enfants. 

Utile aussi pour manipuler les propriétés des jobs.

https://docs.spring.io/spring-batch/reference/job/advanced-meta-data.html#jobregistry

# JobExecutionListener

Extension point to job lifecycle : before (eg. initialize something) and after job execution (eg. cleanup import files doesn't belong to job per se but must be done during batch execution).

1. implement `JobExecutionListener`
2. call `jobBuilder.listener(JobExecutionListener)`

Il est aussi possible d'annoter des méthodes existantes avec @BeforeJob et @AfterJob et d'appeler `jobBuilder.listener(JobListenerFactoryBean.getListener(new MonPojoJobListener()))`.


# ExecutionContext

Equivalent de la session HTTP dans les applications web. \
Permet de trouver les metadata sur l'exécution en cours. \
Sérialisé par `JobRepository` et récupéré par Spring Batch lors d'une reprise d'un job.

Deux niveaux : 
- `JobExecution`
- `StepExecution`

Objet accessible au travers du `ChunkContext` :

```java
chunkContext.getStepContext()
            .getStepExecution()
			.getJobExecution()
			.getExecutionContext();    // contexte d'exécution niveau job
			
chunkContext.getStepContext()
            .getStepExecution()
			.getExecutionContext();    // contexte d'exécution niveau step
```

NB : `stepContext.getJobExecutionContext()` est un raccourci en lecture mais ne persiste pas avec `JobRepository`, contrairement à `jobExecution().getExecutionContext()`.

Il est possible de promouvoir du contexte d'exécution step vers job (à la fin du step) grâce à l'interface `ExecutionContextPromotionListener`. 

```java
@Bean
public StepExecutionListener promotionListener() {
  ExecutionContextPromotionListener listener = new ExecutionContextPromotionListener();
  listener.setKeys(new String[]{"name"});
  return listener;
}

@Bean
public Step step1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
  return new StepBuilder("step1", jobRepository)
	.tasklet(..., transactionManager)
	.listener(promotionListener())
	.build();
}
```

# Filtrage

https://docs.spring.io/spring-batch/reference/processor.html#filteringRecords

Renvoyer `null` au sein de `processor.process(T ...)`.

# Skip
https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring-skip.html

```java
new StepBuilder()
  ...
  .faultTolerant()
  .skipLimit(100)
  .skip(MonException.class)
  ...
```

En fonction de la phase du chunk, le comportement diffère :
* read : on continue à lire le suivant en appelant `next()`
* process : on relance le chunk en écartant l'élément incriminé
* write : on relance les écritures cette fois une par une pour identifier l'élément incriminé 

# Serialiser en l'executionContext en JSON

Depuis la v5, la sérialisation est en base 64. Pour revenir en JSON : 

```java
    @Bean
    public ExecutionContextSerializer executionContextSerializer() {
        return new Jackson2ExecutionContextStringSerializer();
    }
```

Attention, spring batch risque de ne pas démarrer si des executionContexts en base 64 sont déjà présents dans la base de données.

# Late binding

https://docs.spring.io/spring-batch/reference/step/late-binding.html

```java
@JobScope
@StepScope

@Value("#{jobParameters['input.file.name']}") String name
@Value("#{jobExecutionContext['input.name']}") String name
@Value("#{stepExecutionContext['input.file.name']}") String name

@Value("${input.file.name}") String name  // -Dinput.file.name="file://outputs/file.txt"
```

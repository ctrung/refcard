# JobInstance

`JobInstance` : uniquely identified by a job name and a set of identifying parameters

`JobInstanceAlreadyCompleteException` : thrown if a job instance has already executed (can't run more than once)


# Job runner

Point d'entrée pour exécuter un job, appelle JobLauncher derrière.

`CommandLineJobRunner` : utilisé pour les démarrages en ligne de commande
```
java -jar demo.jar name=Michael
java -jar demo.jar executionDate(date)=2017/11/28                 # specify type for JobParameter conversion
java -jar demo.jar executionDate(date)=2017/11/28 -name=Michael   # specify non identifying JobParameter
```	 

`JobLauncherApplicationRunner` : Spring Boot ApplicationRunner to launch Spring Batch jobs \
NB : `JobLauncherCommandLineRunner` est déprécié

`JobParameters` : wrapper around java.util.Map<String, JobParameter>, supporte la conversion via les getters typés


# Accéder au JobParameters

1. Soit via `chunkContext.getStepContext().getJobParemeters()` (nécessite un cast car c'est une `Map<String, Object>`)
2. Soit via du late binding avec `@StepScope`
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

# Validation des JobParameters

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


# JobParametersIncrementer

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

Outil servant d'orchestrateur dans le cloud.

Fonctionnalités :
- API REST
- Déploiement dans le cloud d'application Spring Boot
- IHM Web
- Intégration avec Spring Batch
- Intégration avec Spring Cloud Stream
- Interface avec un shell
- Monitoring
- Orchestration
- Support de plusieurs plateformes : CloudFoundry, Kubernetes, local

# Configuration

Démarrage du serveur : `java -jar spring-cloud-dataflow-server-local-<version>.jar`

Démarrage du shell pour interagir avec le serveur : `java -jar spring-cloud-dataflow-shell-<version>.jar`

# Configuration d'une application à déployer dans Data Flow

Application Spring Batch dans les exemples ci dessous.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-task</artifactId>
</dependency>
```

```java
@EnableTask
@EnableRetry
@EnableBatchProcessing
@EnableDiscoveryClient(autoRegister = false)
public class CloudBatchApplication {
    public static void main(String[] args) {
        SpringApplication.run(CloudBatchApplication.class, args);
    }
}
```

Enregistrement de l'application dans Data Flow, exemple local avec l'artefact Maven. \
Ouvrir une session shell Data Flow et exécuter `register --name <nom de l'application> --type task --uri "maven://<group>:<artefact>:<version>"`

Déclaration de la tâche dans Data Flow. \
Ouvrir une session shell Data Flow et exécuter `task create <nom de la tâche> --definition "<nom de l'application>"`

Plusieurs façon de lancer l'exécution : 
- AdHoc via le shell Data Flow avec la commande `task launch <nom de la tâche> [–-arguments foo=bar ...]` 
- API REST
- Via l'IHM
- Via un ordonnanceur si la plateforme le supporte (CloudFoundry, Kubernetes)

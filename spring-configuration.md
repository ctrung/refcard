# Docs de référence

External Configuration : https://docs.spring.io/spring-boot/reference/features/external-config.html

External Application Properties : https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files

# Fichiers de configuration multiples

Par défaut, le fichier de config Spring Boot est `application.[properties|yaml]`.

Il est possible d'en définir un dans un jar, et un autre à l'extérieur pour complémenter/surcharger les propriétés par environnement par exemple. Cependant, il n'est pas possible d'en définir plusieurs au sein de plusieurs jars. A la place, Spring Boot permet :
- Le mélange de fichiers properties et yaml car l'unicité du nom tient compte de l'extension. Cette solution n'est pas recommandée, et ne permet que d'avoir qu'un seul fichier de conf supplémentaire.
- Mise en place d'un sous dossier `config/`.
- Import d'un ou plusieurs fichiers avec `spring.config.import`. C'est la méthode recommandée depuis Spring Boot 2.4.
- Redéfinition de l'emplacement des fichiers config avec `spring.config.location` ou encore `spring.config.additional-location`. En pratique, l'option est définie en ligne de commandes, ex : `java -jar myproject.jar --spring.config.location=optional:classpath:/default.properties,optional:classpath:/override.properties` pour être interprétée avant la lecture des properties.

Cas particulier des tests : 

`/src/test/resources/application.yaml` est pris en compte à la place de `/src/java/resources/application.yaml` si présent. Pour surcharger/complémenter, on peut toujours recourir à un `/src/test/resources/config/application.yaml` comme décrit au dessus.

# Configurations imbriquées

https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files.importing

Propriété `spring.config.import` avec les fonctionnalités suivantes supportées :
- Optionnel : `spring.config.import=optional:file:./dev.properties`
- Plusieurs (le dernier gagne) : `spring.config.import=optional:file:./dev1.properties,optional:file:./dev2.properties`
- Hint fichier sans extension (utile cloud) : `spring.config.import=file:/etc/config/myconfig[.yaml]`
- Configuration tree (utile cloud) : `spring.config.import=optional:configtree:/etc/config/`

# Docs de référence

External Configuration : https://docs.spring.io/spring-boot/reference/features/external-config.html

External Application Properties : https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files

# Tests Spring

Il y a deux façons de définir un `application.yaml` spécifique au tests sans passer par un profil spring : 
1. `/src/test/resources/application.yaml` pour **remplacer** celui de `/src/java/resources/application.yaml`
2. `/src/test/resources/config/application.yaml` pour **complémenter/surcharger** celui de `/src/java/resources/application.yaml`

# Configurations imbriquées

https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files.importing

Propriété `spring.config.import` avec les fonctionnalités suivantes supportées :
- Optionnel : `spring.config.import=optional:file:./dev.properties`
- Plusieurs (le dernier gagne) : `spring.config.import=optional:file:./dev1.properties,optional:file:./dev2.properties`
- Hint fichier sans extension (utile cloud) : `spring.config.import=file:/etc/config/myconfig[.yaml]`
- Configuration tree (utile cloud) : `spring.config.import=optional:configtree:/etc/config/`

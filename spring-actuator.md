## Exposition

Par défaut, pas grand chose est exposé (sécurité oblige).

Exemples de configuration :

```yaml

management.endpoints.web.exposure.include: "health, beans"    # inclut les endpoints demandés
management.endpoints.web.exposure.exclude: "health, beans"    # exclut les endpoints demandés

management.endpoints.web.exposure.include: "*"                # inclut tout, bien mettre * entre
                                                              # quotes doubles car mot réservé en yaml
management.endpoints.web.exposure.exclude: "*"                # exclut tout
```

L'algorithme prend d'abord en compte les `include`, puis les `exclude`.

Doc : https://docs.spring.io/spring-boot/reference/actuator/endpoints.html#actuator.endpoints.exposing

Liste des endpoints disponibles : https://docs.spring.io/spring-boot/api/rest/actuator 

## Activer les indicateurs

Par défaut, l'endpoint `health` n'affiche pas grand chose (sécurité oblige).

Exemple de configuration :

```yaml
management.endpoint.health.show-details: "always"  # affiche tout (par défaut `never`,
                                                   # autre valeur possible : `when-authorized`
                                                   # qui se base sur les rôles)

management.health.<key>.enabled: false             # désactive un composant (activé par défaut)

management.health.defaults.enabled: false          # désactive tous les composants (true par défaut)

```

Doc : https://docs.spring.io/spring-boot/reference/actuator/endpoints.html#actuator.endpoints.health

Liste des composants : https://docs.spring.io/spring-boot/reference/actuator/endpoints.html#actuator.endpoints.health.auto-configured-health-indicators

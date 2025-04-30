
## Prendre en compte le coverage du code provenant d'un autre module

**Usecase**

- Projet Maven multi-modules
- Test d'intégration qui exécute du code d'autres sous-modules

Par défaut, les rapports JaCoCo ne tiennent compte que du coverage du code du sous module courant. \
Si un test (d'intégration) couvre le code d'un autre sous module, son coverage n'est comptabilisé dans aucun rapport.


**Solution**

Utiliser le goal `report-aggregate` introduit dans la version 0.7.7 et qui permet aussi de générer un seul rapport global.
Le goal est à appeler depuis un sous-module qui référence tous les autres sous modules voulus (peut être dédié ou le dernier sous-module construit au sein de reactor).

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <id>report-aggregate</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>report-aggregate</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

**Bonus**

Pour que SonarQube référence cet unique rapport, utiliser la propriété suivante.

```xml
<sonar.coverage.jacoco.xmlReportPaths>
    ${maven.multiModuleProjectDirectory}/<SOUS_MODULE>/target/site/jacoco-aggregate/jacoco.xml
</sonar.coverage.jacoco.xmlReportPaths>
```

**Réfs**

- [Page MavenMultiModule du wiki de JaCoCo](https://github.com/jacoco/jacoco/wiki/MavenMultiModule)
- [Doc SonarQube pour analyser un projet Maven multi-modules avec JaCoCo](https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/test-coverage/java-test-coverage/#multi-module-maven-project)
- [Doc du goal report-aggregate](https://www.jacoco.org/jacoco/trunk/doc/report-aggregate-mojo.html)

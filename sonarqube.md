
## Analysis

```
mvn sonar:sonar -Dsonar.host.url=... -Dsonar.login=... -Dsonar.password=...
mvn sonar:sonar -Dsonar.host.url=... -Dsonar.token=...
```

Optionals : `-Dsonar.projectKey=...`, `-Dsonar.projectName=...`


## Modules can't have the same key during analysis

Happens when projectKey is defined without moduleKey in parent pom for multi-modules project.

Error is : 
```sh
[ERROR] Failed to execute goal org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.1.2184:sonar (default-cli) on project XXX-parent: Project 'XXX' can't have 2 modules with the following key: XXX -> [Help 1]
```

```xml
<sonar.projectKey>XXX</sonar.projectKey>
<sonar.moduleKey>${artifactId}</sonar.moduleKey>   <!-- add this line to resolve --> 
 ```

# Reindex

```sh
cd $SONAR_HOME
rm -rf data/es6/ # or es<version>
```

# Rename projects in database

```sql
-- dbo.projects
UPDATE <DB>.dbo.projects SET name = N'...' WHERE kee = N'...' ESCAPE '#';
UPDATE <DB>.dbo.projects SET name = N'...' WHERE kee = N'...' ESCAPE '#';

-- dbo.components
UPDATE <DB>.dbo.components SET name = N'...', long_name = N'...' WHERE scope = N'PRJ' AND kee = N'...' ESCAPE '#';
UPDATE <DB.>dbo.components SET name = N'...', long_name = N'...' WHERE scope = N'PRJ' AND kee = N'...' ESCAPE '#';
```

`name` is `sonar.projectName` \
`kee` is `sonar.projectKey` \
`long_name` is not used anymore ? just put same as `name`   



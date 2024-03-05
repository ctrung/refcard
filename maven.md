## Default version of plugins

See : https://maven.apache.org/ref/X.Y.Z/maven-model-builder/super-pom.html \
Eg. https://maven.apache.org/ref/3.5.3/maven-model-builder/super-pom.html

Examples : 
- maven-release-plugin 2.5.3 for maven 3.5.3
- maven-release-plugin 2.3.2 for maven 3.5.0

## maven-assembly-plugin

### No filters for binary files

Since 3.3.0+ (https://issues.apache.org/jira/browse/MASSEMBLY-849)

```xml
<fileset>
  <directory>${project.basedir}</directory>
  <outputDirectory>${project.artifactId}</outputDirectory>
  ...
  <lineEnding>unix</lineEnding>                               <!-- this might rewrite files -->
  <filtered>true</filtered>                                   <!-- this too -->
  ...
  <nonFilteredFileExtensions>                                 <!-- but it won't affect files with these extensions -->
    <nonFilteredFileExtension>png</nonFilteredFileExtension>
  </nonFilteredFileExtensions>
</fileSet>
```

Before 3.3.0

```xml
<fileSet>                                                      <!-- fileset with rewriting (lineEnding, filtered, etc...) : text files -->
  <directory>${project.basedir}</directory>
  <outputDirectory>${project.artifactId}</outputDirectory>
  <fileMode>0775</fileMode>
  <lineEnding>unix</lineEnding>
  <excludes>
    <exclude>*.png</exclude>
  </excludes>
</fileSet>
<fileSet>                                                      <!-- fileset with no rewriting : binary files -->
  <directory>${project.basedir}</directory>
  <outputDirectory>${project.artifactId}</outputDirectory>
  <fileMode>0555</fileMode>
  <includes>
    <include>*.png</include>
  </includes>
</fileSet>	
```

## maven-deploy-plugin

A savoir : option <skip> supportée par le goal deploy-file depuis la 3.1.0. 

```xml
<execution>
	<id>deploy-release-patch-tar.gz</id>
	<phase>deploy</phase>
	<goals>
		<goal>deploy-file</goal>
	</goals>
	<configuration>
		<url>http://.../nexus/content/repositories/...</url>
		<repositoryId>nexus</repositoryId>
		<file>target/${project.artifactId}-${project.version}-patch.tar.gz</file>
		<groupId>${project.groupId}</groupId>
		<artifactId>${project.artifactId}</artifactId>
		<version>${project.version}</version>
		<classifier>patch</classifier>
		<packaging>tar.gz</packaging>
		<generatePom>false</generatePom>
	</configuration>
</execution>
```

## maven-release-plugin

```sh
mvn \
-Dusername=... \
-Dpassword=... \
-DdevelopmentVersion=... \
-DreleaseVersion=... \
-Darguments="-DskipTests=true -Dmaven.javadoc.skip=true" \
-DtagNameFormat="v$releaseVersion" \
-B $MVN_ARGS release:prepare release:perform
```

SCM credentials used for tag, checkin or push during release can be specified :
- on command line : `-Dusername`, `-Dpassword`
- in `settings.xml`, in `<server/>` tag

Example of `<server/>` in `settings.xml` :

```xml
  <servers>
    <server>
      <id>nexus</id>            <!-- this id should match <repositoryId> in maven-deploy-plugin's configuration -->
      <username>...</username>
      <password>...</password>
    </server>
    <server>
       <id>dev.azure.com</id>   <!-- this id should match host in <scm>/<connection> and <scm>/<developerConnection> sections of pom.xml -->
       <username>...</username>
       <password>...</password>
    </server>
  </servers>
```

More details on how to allow specific credentials : https://issues.apache.org/jira/browse/SCM-826

pom.xml
```xml
<properties>
  <project.scm.id>my-scm-server<project.scm.id>
</properties>
```

settings.xml
```xml
<settings>  
   <servers>  
      <server>
         <id>my-scm-server</id>  
         <username>myUser</username>  
         <password>myPassword</password>  
      </server>   
   </servers>
</settings>
```




## versions-maven-plugin

https://stackoverflow.com/questions/5726291/updating-version-numbers-of-modules-in-a-multi-module-maven-project

```sh
mvn versions:set -DnewVersion=2.50.1-SNAPSHOT
```

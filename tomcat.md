## Customize webapp config through context.xml file

Example `$TOMCAT_HOME/conf/context.xml` or `$TOMCAT_HOME/conf/ENGINE/VHOST/APP.xml`

```xml

<?xml version='1.0' encoding='utf-8'?>

<Context>

    <WatchedResource>WEB-INF/web.xml</WatchedResource>
    <WatchedResource>${catalina.base}/conf/web.xml</WatchedResource>

    <Resource name="jdbc/xxx" auth="Container" type="javax.sql.DataSource" maxTotal="100" maxIdle="30" maxWaitMillis="10000"
              username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
              url="jdbc:mysql://HOST:3306/BDD_metier?zeroDateTimeBehavior=CONVERT_TO_NULL&amp;serverTimezone=Europe/Paris" />

    <Resource name="jdbc/xxx" auth="Container" type="javax.sql.DataSource"
              maxTotal="200" maxIdle="50" maxWaitMillis="10000" username="USERNAME"
              password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
              url="jdbc:mysql://HOST:3306/BDD_metier" validationQuery="SELECT 1" />

    <Resource name="java:/comp/env/jdbc/yyy" auth="Container" driverClassName="com.mysql.jdbc.Driver"
              maxActive="200" maxIdle="50" maxWait="10000" 
              password="PASSWORD" type="javax.sql.DataSource"
              url="jdbc:mysql://HOST/BDD_doc?useUnicode=true&amp;characterEncoding=UTF-8&amp;useFastDateParsing=false&amp;autoReconnect=true"
              username="USERNAME" validationQuery="SELECT 1" />
				
    <Resource name="jdbc/yyy" auth="Container" driverClassName="com.mysql.jdbc.Driver"
              maxTotal="200" maxIdle="50" maxWaitMillis="10000" 
              password="PASSWORD" type="javax.sql.DataSource"
              url="jdbc:mysql://HOST:3306/BDD_doc" username="USERNAME" validationQuery="SELECT 1" />

    <Resource name="jdbc/yyy" auth="Container" type="javax.sql.DataSource"
              maxTotal="100" maxIdle="30" maxWaitMillis="10000"
              username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
              url="jdbc:mysql://HOST:3306/BDD_doc?zeroDateTimeBehavior=CONVERT_TO_NULL&amp;serverTimezone=Europe/Paris" />

    <Resources className="org.apache.catalina.webresources.StandardRoot">
        <PreResources className="org.apache.catalina.webresources.DirResourceSet"
                      base="/path/to/config/dir"
                      internalPath="/"
                      webAppMount="/WEB-INF/classes" />
    </Resources>

    <Environment name="spring.config.additional-location"
                 type="java.lang.String"
                 value="/path/to/spring/application.yml" />

</Context>

```

## Deploy by calling manager remote API with curl

```shell
# undeploy
curl --user $TOM_USER:$TOM_PASS http://<host>:<port>/manager/text/undeploy?path=/<ctx>

# deploy by uploading local war file
curl --user $TOM_USER:$TOM_PASS --upload-file <file.war> http://<host>:<port>/manager/text/deploy?path=/<ctx>

# deploy a war file present on the tomcat server
curl --user $TOM_USER:$TOM_PASS http://<host>:<port>/manager/text/deploy?path=/<ctx>&war=file:<war file full path on the tomcat server>

# deploy a war file and a context file present on the tomcat server
curl --user $TOM_USER:$TOM_PASS http://<host>:<port>/manager/text/deploy?path=/<ctx>&war=file:<war file full path on the tomcat server>&config=file:<context file full path on the tomcat server>
```

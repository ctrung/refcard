## Liens

https://docs.spring.io/spring-boot/reference/features/logging.html

https://docs.spring.io/spring-boot/how-to/logging.html


## Couleurs dans la console 

spring.output.ansi.enabled=ALWAYS|DETECT|NEVER

## Extensions logback

https://docs.spring.io/spring-boot/reference/features/logging.html#features.logging.logback-extensions

Les tags `<springProfile>` et `<springProperty>`.


## Db appender custom logback

Fichier de conf `logback-spring.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml"/>

    <springProperty name="DATASOURCE_URL" source="spring.datasource.url"/>
    <springProperty name="DATASOURCE_USERNAME" source="spring.datasource.username"/>
    <springProperty name="DATASOURCE_PASSWORD" source="spring.datasource.password"/>

    <appender name="DB" class="mon.package.DbAppender">
        <dataSource class="com.zaxxer.hikari.HikariDataSource">
            <jdbcUrl>${DATASOURCE_URL}</jdbcUrl>
            <username>${DATASOURCE_USERNAME}</username>
            <password>${DATASOURCE_PASSWORD}</password>
        </dataSource>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="GED_DB"/>
    </root>

</configuration>
```

Java

```java
public class DbAppender extends AppenderBase<ILoggingEvent> {

    @Override
    public void start() {
      // ...
    }

    @Override
    protected void append(ILoggingEvent eventObject) {
      Level level = eventObject.getLevel();
      ...
    }
}
```

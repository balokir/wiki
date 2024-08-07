# Hibernate and Spring Boot - show sql with parameters.

There are a lot of articles about it. The best, from me is https://www.baeldung.com/sql-logging-spring-boot

basic information:
* Property `show-sql=true` produces output to `std.out` but I prefer logger,
because query parameters can be displayed via logger only.  
* There are useful properties `format_sql` and `use_sql_comments`

## Logging SQL in Spring boot 3 with Hibernate 6
https://stackoverflow.com/a/74862954/3864309

Use `logging.level.sql` for SQL logging group including Hibernate SQL logger and 
`logging.level.org.hibernate.orm.jdbc.bind` for showing the binding parameters.

So this should work:
````yaml
logging:
  level:
    sql: debug
    org.hibernate.orm.jdbc.bind: trace
````

## My current configuration:

* Spring Boot v. 2.6.2
* Hibernate v. 5.6.3.Final
* Logging via Logback



## What works for me:

* Using interceptors - for example "com.github.gavlyukovskiy:datasource-proxy-spring-boot-starter"  
Works perfectly, but logs all JDBC queries, including liquidbase which decrease startup performance.
* Hibernate properties via JPA
    ````yaml
      jpa:
        properties:
          hibernate:
            format_sql: true
            use_sql_comments: true
    ````
* Logger settings
    ````xml
        <!-- show SQL -->
        <logger name="org.hibernate.SQL" level="DEBUG" additivity="false">
            <appender-ref ref="STDOUT"/>
        </logger>
    
        <!-- show parameters binding -->
        <logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE" additivity="false">
            <appender-ref ref="STDOUT"/>
        </logger>
    ````

## Troubleshooting

I spent several hours to understand why logger `org.hibernate` does not work. 
When I was debugging BasicBinder, I saw that the logger was set to ERROR level regardless of the settings.

The solution was found here https://stackoverflow.com/a/19488546/3864309, but not exactly as suggested.

`org.jboss.logging.LoggerProviders` tries to discover logger via classpath. 
I had `log4j-api` in classpath and provider was using it instead of logback. 
I just excluded `log4j` and it solved the problem.

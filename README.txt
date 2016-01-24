The application is made up of 5 maven projects:

sky-webapp-app
sky-webapp-ear
sky-webapp-ejb
sky-webapp-jpa
sky-webapp-view

These should all sit in parallel directories and can be built using maven, by running "mvn install" agains the "sky-webapp-app" project. The JUnit tests will be run as part of this. The important output will be an EAR file created at "sky-webapp-ear\target\sky-challenge.ear". This can be deployed on a Java web container, specifically it has been tested against JBoss EAP 6.4.

The solution relies on a mysql database with the following tables:

news_product
sports_product

These tables can be created as follows:

CREATE TABLE `news_product` (
  `id` int(11) NOT NULL,
  `name` varchar(45) NOT NULL,
  PRIMARY KEY (`id`)
) 

CREATE TABLE `sports_product` (
  `id` int(11) NOT NULL,
  `name` varchar(45) NOT NULL,
  `location_id` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
)

insert into news_product values (1, 'Sky News');
insert into news_product values (2, 'Sky Sports News');

insert into sports_product values (1, 'Arsenal TV', 1);
insert into sports_product values (2, 'Chelsea TV', 1);
insert into sports_product values (3, 'Liverpool TV', 2);

To change the database settings refer to persistence.xml in the JPA project. Since hibernate is used it should be possible to use a variety of database servers if required. Note that the datasource should exist in JNDI on the application server at:

java:jboss/datasources/skyDS

An example configuration is shown below:

        <subsystem xmlns="urn:jboss:domain:datasources:1.2">
            <datasources>
                <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE</connection-url>
                    <driver>h2</driver>
                    <security>
                        <user-name>sa</user-name>
                        <password>sa</password>
                    </security>
                </datasource>
                <datasource jndi-name="java:jboss/datasources/skyDS" pool-name="skyDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:mysql://localhost:3306/sky</connection-url>
                    <driver>mysql</driver>
                    <security>
                        <user-name>root</user-name>
                        <password>mysql</password>
                    </security>
                </datasource>
                <drivers>
                    <driver name="h2" module="com.h2database.h2">
                        <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
                    </driver>
                    <driver name="mysql" module="com.mysql">
                        <xa-datasource-class>com.mysql.jdbc.Driver</xa-datasource-class>
                    </driver>
                </drivers>
            </datasources>
        </subsystem>
		
Note this requires the creation of a mysql module on jboss, called com.mysql that refers to "mysql-connector-java-5.1.38.jar"

For example i have created a folder here:


C:\application-servers\jboss-eap-6.4\modules\com\mysql\main

containing the aforementioned JAR and a module.xml with the following contents:

<?xml version="1.0" encoding="UTF-8"?>
 
<module xmlns="urn:jboss:module:1.0" name="com.mysql">
  <resources>
    <resource-root path="mysql-connector-java-5.1.38.jar"/>
  </resources>
  <dependencies>
    <module name="javax.api"/>
  </dependencies>
</module>

Note the best URL to access the application is:

http://localhost:8080/sky-webapp-view/test-harness.jsf

This will allow you to set the cookie value prior to accessing the product selection page.



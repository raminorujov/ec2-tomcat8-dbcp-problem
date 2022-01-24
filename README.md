# AWS Amazon Linux AMI Tomcat 8 DBCP problem solutionAWS Amazon Linux AMI Tomcat 8 DBCP problem solution

I encountered this issue in 2018, while deploying my app on AWS EC2. 
If you use "64bit Amazon Linux 2017.09 v2.7.5" (EC2 image ID: ami-69000209), embedded Tomcat 8 doesn't contain  DBCP database connection pool jar files, your JNDI datasource database connection will fail with the exception
java.lang.ClassNotFoundException: org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory

## Solution steps
### Step1. Add the following maven dependencies( Apache Commons DBCP 2.x and  Apache Commons DBCP 2.x).

    

    <dependency>
    	<groupId>org.apache.commons</groupId>
    	<artifactId>commons-dbcp2</artifactId>
    	<version>2.2.0</version>
    </dependency>
    <dependency> 
    	<groupId>org.apache.commons</groupId>
    	<artifactId>commons-pool2</artifactId>
    	<version>2.5.0</version>
    </dependency>

If you don't use Maven, you can copy commons-dbcp2-2.2.0.jar and commons-pool2-2.5.0.jar files into project's WEB-INF/lib folder.

### Step 2. Add factory="org.apache.commons.dbcp2.BasicDataSourceFactory" into your  META-INF/context.xml files.


    <?xml version="1.0" encoding="UTF-8"?>
    <Context>
        <Resource name="jdbc/mdbwebdb"
                  auth="Container"
                  type="javax.sql.DataSource"
                  username="db-user"
                  password="db-pwd"
                  driverClassName="jdbc-driver"
                  url="jdbc-url"
                  factory="org.apache.commons.dbcp2.BasicDataSourceFactory"
                  initialSize="2"
                  maxTotal="20"
                  minIdle="2"
                  maxIdle="20"
                  maxWaitMillis="3000"
                  defaultAutoCommit="false"
                  timeBetweenEvictionRunsMillis="30000"
                  removeAbandonedOnBorrow="true"
                  removeAbandonedOnMaintenance="true"
                  removeAbandonedTimeout="60"
                  logAbandoned="true"
        ></Resource>
    </Context>

Please, consider that some properties are renamed.

**maxActive = maxTotal
maxWait = maxWaitMillis
removeAbandoned = removeAbandonedOnBorrow, removeAbandonedOnMaintenance
**

## References:
1.[https://forums.aws.amazon.com/thread.jspa?messageID=721145](https://forums.aws.amazon.com/thread.jspa?messageID=721145 "https://forums.aws.amazon.com/thread.jspa?messageID=721145")
2.[http://tomcat.apache.org/migration-8.html#Database_Connection_Pooling](http://tomcat.apache.org/migration-8.html#Database_Connection_Pooling "http://tomcat.apache.org/migration-8.html#Database_Connection_Pooling")
3.[http://commons.apache.org/proper/commons-dbcp/configuration.html](http://commons.apache.org/proper/commons-dbcp/configuration.html "http://commons.apache.org/proper/commons-dbcp/configuration.html")

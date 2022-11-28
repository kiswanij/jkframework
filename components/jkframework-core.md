
# JKFramework-Core

It is a set of API's, utilites, and wrapper that enables faster and reliable applications development, such as configurations, exception handling, context, I/O, and many others, and which are the intensivly used by the other projects in the framework

## Configuration
This section includes the user-guide of JK-Framework configurations.

**Configuration properties**
Please check the below link for more details about the list of available configs properties used by JK-Framework:
Available properties.

**Configuration Order**
In JKFramework, the configuration are loaded based on the following priorities:
- Load smartcloud.config file if exists from current working directory, and override and previous configuration [Starting from 5.1.0].
- Load configuration from default.config.properties (not recommended to use, since its already available in the framework data API’s).
- Load configuration from system.config.properties and override and previous configuration.
- Load configuration from app.config.properties and override and previous configuration.
- Load configuration from config.properties and override and previous configuration.
- Load the keys directly from host environment variables, for example, you can set the hibernate.connection.username as environment variable key, individually and override and previous configuration.
- Load SMART_CLOUD environment variable if exists, where the value is CSV key-value format for the configuration, and override and previous configuration.
- Load from CloudFoundery VCAP_SERVICES environment variable if exists, and override and previous configuration.
- Process pre.config.properties which shall contains a local path for the config and/or Git repository settings (check the below section). In this file:
- Look for app.name property, if not found, read it from previous configurations.
- If config.local.path is available, read system.config.properties and ${app.name}.config.properties from it.
- if git-url is there, repository will be cloned to local folder (${config.local.path} if configured).
- Read system.config.properties and ${app.name}.config.properties from cloned repository.
- If git-keep-local property is false (default), the cloned local folder will be deleted.
- Load test.config.properties file if exists from the classpath, and override and previous configuration.


loading config activity diagram
Figure 1. Flowchart of the load configuration process.
Load Configuration from Local Folder or Git Repository
Starting from 4.0.9, you can specify a Git repository to host your configurations per app.

You can do it by adding a pre.config.properties to your src/main/resources folder of your project.

app.name=....
config.local.path=....
git-url=....
git-username=....
#git-password=.....
git-password-plain=.....
In your repository, you can have system.config.properties file as default configuration for all the apps in your applications, also you can specify the configuration per module by having a file named ${app.name}.config.properties where ${app.name} is the value of your app.name property in the pre.config.properties mentioned above.

For profiles, you can use git branches as profiles, for example, you can create dev,staging, and prod branches.

If you have multiple projects and you don’t want to place the git repotisoty configuration in each project, you can put the pre.config.properties in a commons lib project, and have app.config.properties in each project with the app.name key only.

To have more flexibility and security, you can replace your config.properties or "pre.config.properties" during the CICD process to have the right configuration to avoid exposing production and other environments configurations to the wrong people.

Smart-Cloud configuration is based on Apache Commons Config, so you can use the system, constants, or environment variables as follows:

user.file = ${sys:user.home}/settings.xml
action.key = ${const:yourclass.CONSTANT_NAME}
java.home = ${env:JAVA_HOME}
Basic Usage
Create src/main/resources/config.properties file inside your root source folder, in case of maven projects, with the following sample contents:

#H2 Configurations
hibernate.connection.driver_class = org.h2.Driver
hibernate.connection.url = jdbc:h2:file:./h2db.data

#MySQl Config
#hibernate.connection.driver_class = com.mysql.jdbc.Driver
#hibernate.connection.url = jdbc:mysql://localhost:3306/app?createDatabaseIfNotExist=true

#Oracle Config, you will need to add oracle driver dependncy,
#checkout, option 4(System Path): https://www.mkyong.com/maven/how-to-add-oracle-jdbc-driver-in-your-maven-local-repository/

#hibernate.connection.driver_class = oracle.jdbc.driver.OracleDriver
#hibernate.connection.url = jdbc:oracle:thin:@localhost:1521/orclpdb1
#hibernate.c3p0.preferredTestQuery=SELECT 1 FROM DUAL

hibernate.connection.username = sa
hibernate.connection.password = sa

#To build tables from Entities
hibernate.hbm2ddl.auto=update
#Package to scan for JPA entities, CSV value is allowed for multiple packages
db-entities-packages=com.app
Config Initialization
In most cases, configuration will be auto initlized and loaded during the application startups using listeners. However, if for some reason you want to initialize it your self, the first call to JKConfig.getDefaultInstance() will auto configure it.

And to retrieve value from the configuration, use the following code:

String property = JKConfig.getDefaultInstance().getProperty("your-variable-name", "default value);
Multi-Database Support
In the config file, you can add a prefix to the same configuration used in normal database setup. Then you can call the

mydb.hibernate.connection.driver_class = oracle.jdbc.driver.OracleDriver
mydb.hibernate.connection.url =jdbc:oracle:thin:@0.0.0.0:1521:DB
mydb.hibernate.dialect =org.hibernate.dialect.Oracle12cDialect
mydb.hibernate.connection.username =user
mydb.hibernate.connection.password = pass
Microservices
jk.service.crosscutting.logservice.enabled=false jk.service.crosscutting.logservice.base jk.service.crosscutting.logservice.async=false jk.service.url.visibleOnError=true

Access Properties
Properties are all used internally by the framework, but if you need for any reason, you can access any property, use

Prior to versions 1-3, all the configuration was set on System properties, and could retrieved by:

System.getProperty(propertyName);
In version 4, it should be retrieved like this:

JKConfig.getDefaultInstance().getProperty(propertyName);
Home | License | FAQ | Contact | Smart-API
All copyrights reserved to Dr. Jalal Kiswani.
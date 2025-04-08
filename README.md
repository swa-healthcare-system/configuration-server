# Spring Cloud Configuration Server

## Description
Spring Cloud Configuration Server implement pattern of externalized configuration in microservices.

Git repository https://github.com/swa-healthcare-system/configuration-storage is used to store  all configuration files.

Client microservices can load their configuration files from the server.

## Configuration storage
Configurations are stored here https://github.com/swa-healthcare-system/configuration-storage.

To update it you need just to push file.

There is a convention that spring.application.name of your service has to match
config file which the server will return. So if your microservice has name
spring.application.name=doctor-service then server will look for doctor-service.yml file.

## Client setup

### Spring boot
To set up spring boot microservice client you need to add this into pom.xml:

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>2024.0.1</version>
            <type>pom</type>
            <scope>import</scope>
            </dependency>
    </dependencies>
</dependencyManagement>
```

```
<dependencies>
    ...
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
    ...
</dependencies>
```

Then you need to configure config server in application.yml/application.properties:
Configuration file name should match name of spring application! 
```
spring:
  application:
    name: doctor-service
  config:
    # change host to match yours.
    # Use host.docker.internal as host if configuration-server runs locally and service inside docker
    import: configserver:http://config-server:8888
```


### Quarkus
Here is guide how to set up quarkus client: https://quarkus.io/guides/spring-cloud-config-client

Basically you need to add this dependency:
```
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-spring-cloud-config-client</artifactId>
</dependency>
```
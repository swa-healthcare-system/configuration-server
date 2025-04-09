# Spring Cloud Configuration Server

## Description
Spring Cloud Configuration Server implement pattern of externalized configuration in microservices.

Git repository https://github.com/swa-healthcare-system/configuration-storage is used to store  all configuration files.

Configuration server connects to configuration-storage and follows changes of configs there.

Client microservices can load their configuration files via config-server.

## Configuration storage
Configurations are stored here https://github.com/swa-healthcare-system/configuration-storage.

To update it you need just to push updated config file.

**There is a convention that spring.application.name of your service has to match
config file which the server will return. So if your microservice has name
spring.application.name=doctor-service then server will look for doctor-service.yml
or doctor-service.properties file.**

## Run configuration server
docker-compose up --build

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

You need to add this dependency to pom.xml:
```
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-spring-cloud-config-client</artifactId>
</dependency>
```
and put this into application.properties:
```
quarkus.application.name=service-name
quarkus.spring-cloud-config.enabled=true
quarkus.spring-cloud-config.url=http://config-server:8888
quarkus.log.category."io.quarkus.spring.cloud.config".level=DEBUG
quarkus.spring-cloud-config.profiles=default
```

## Profiles
One of the main concepts of config-server is that you can store configs for
different profiles such as test, dev, prod etc.

Spring boot by default uses "default" profile so there is no need to substantially
modify config name in the config storage and no need to specify profiles in 
application.yml/application.properties.

Quarkus looks up "prod" profile by default, so to keep config names simple it is important
to put this quarkus.spring-cloud-config.profiles=default into application.yml/application.properties

## Testing without config-server
Disabling of server does not work well, so if you want to test with local config props
just comment out part related to server
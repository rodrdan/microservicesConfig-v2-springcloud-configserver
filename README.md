Adding config-server

1. Create a new project using Spring initializr with config server and actuator dependencies
2. ConfigServerApplication.java -> add @EnableConfigServer
3. Config server application.properties -> change to application.yml + add properties
    - server port
    - server name
4.
   1. How to read configurations - method 1 - from classpath
       - save properties files to a separate configserver project resource directory:
       - application.yml -> spring: profiles: active: native + search-locations: (**classpath** address)
   2. How to read configurations - method 2 - from file system location:
        - save properties files to a local directory, e.g. $USER\Documents\config
        - application.yml -> spring: profiles: active: native + search-locations: (**file** address)
   3. How to read configurations - method 3 - from a GitHub repository (**RECOMMENDED**)
        - save properties in a GitHub repo
        - application.yml -> spring: profiles: active: git + cloud: config: server: git:
5. Build -> URLs to get properties based on profiles:
    - http://localhost:8071/accounts/default
    - http://localhost:8071/accounts/qa
    - http://localhost:8071/accounts/prod
    - http://localhost:8071/cards/default
    - http://localhost:8071/cards/qa
    - http://localhost:8071/cards/prod
    - http://localhost:8071/loans/default
    - http://localhost:8071/loans/qa
    - http://localhost:8071/loans/prod
6. Connecting individual MS to config-server:
    - leave only default profile application.yml (delete qa, prod etc.) in the project
    - give a name to MS: application.yml -> spring: application: name: "accounts" (for instance)
    - pom.xml -> add <spring-cloud.version>, spring-cloud-starter-config dependency and 
       <dependencyManagement> (spring-cloud-dependencies)
    - application.yml -> keep only properties not related to different environments, 
       add spring: config: import (optional=app will still start even when config-server is not available)
7. Configuration properties are now available to the controller and we can use MS REST endpoints.
   
Adding property values encryption

1. application.yml -> encrypt: key: (_add your key_)
2. REST endpoints:
    - http://localhost:8071/encrypt (encrypts value in body by using key)
    - http://localhost:8071/decrypt (decrypts encrypted value in body to plaintext)

Adding refresh configurations (without app restart)

1. Using actuator path:
   - records DTOs -> class DTOs
   - application.yml -> add management: endpoints...
   - change property value in GitHub repo
   - invoke new endpoint http://localhost:8080/actuator/refresh
   - each change means invoking endpoint again (= **NOT RECOMMENDED**)
2. Using Spring Cloud Bus + message broker (RabbitMQ):
  - docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
  - MS pom.xml -> spring-cloud-starter-bus-amqp dep. + spring-boot-starter-actuator dep.
  - MS application.yml -> add management: endpoints... + add rabbitmq: ...
  - change property value in GitHub repo
  - invoke new endpoint http://localhost:8080/actuator/busrefresh of ANY instance of ANY MS
  - all changes are loaded in all MS (only need to invoke endpoint once = **RECOMMENDED**)
3. Using Spring Cloud Bus + Spring Cloud Monitor with webhook
    - config-server pom.xml -> add spring-cloud-config-monitor dep.
    - config-server application.yml -> add management: endpoints... + add rabbitmq: ...
    - create a webhook in GitHub with monitoring URL
    - change property value in GitHub repo
    - rest is done automatically - no need to invoke any endpoint manually

Creating Docker images
1. Create new docker-compose directories and files
2. docker-compose.yml -> environment: SPRING_CONFIG_IMPORT: ... + SPRING_PROFILES_ACTIVE: ...
3. Config-server application.yml -> Set-up liveness and readiness properties (endpoints) on config-server
    endpoints:
        - http://localhost:8071/actuator/health
        - http://localhost:8071/actuator/health/liveness
        - http://localhost:8071/actuator/health/readiness
4. Config-server docker-compose.yml -> Set-up healthchecks + dependencies of containers
5. Create images using mvn compile jib:dockerBuild




   
1. ConfigServerApplication.java -> add @EnableConfigServer
2. application.properties -> change to application.yml + add properties
    - server port
    - server name
3.1 How to read configurations - method 1 - from classpath
    - save properties files to a separate configserver project resource directory
    - application.yml -> spring: profiles: active: native
3.2 How to read configurations - method 2 - from file system location
3.3 How to read configurations - method 3 - from a GitHub repository
4. build -> URLs to get properties based on profiles:
    - http://localhost:8071/accounts/default
    - http://localhost:8071/accounts/qa
    - http://localhost:8071/accounts/prod
    - http://localhost:8071/cards/default
    - http://localhost:8071/cards/qa
    - http://localhost:8071/cards/prod
    - http://localhost:8071/loans/default
    - http://localhost:8071/loans/qa
    - http://localhost:8071/loans/prod
5. Connecting individual MS:
    - give a name to MS: application.yml -> spring: application: name: "accounts" (for instance)
    - pom.xml -> add <spring-cloud.version>, spring-cloud-starter-config dependency and <dependencyManagement> (spring-cloud-dependencies)
    - application.yml -> spring: config: import (optional=app will still start even when configserver is not available)
   
   
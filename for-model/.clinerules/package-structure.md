Please create the project based on the structure below.

Root Structure (including root POM)
    {{options.package}}
    ├── pom.xml                                         ← Root POM (packaging: pom)
    ├── common-module/                                  ← Common domain library, shared DTOs, exceptions, configurations
    │   └── pom.xml
    ├── Aggregate-module/                       ← Aggregate domain module
    │   ├── src/main/java/com/example/{Aggregate name}
    │   │   ├── domain
    │   │   │   │   ├── event
    │   │   │   │   │   └── Event.java                  ← Event Publish class for the results of domain state changes in the Aggregate Root
    │   │   │   │   └── repository                      
    │   │   │   │       └── {Aggregate name}Repository.java   ← JPA Repository
    │   │   │   ├── {Aggregate name}.java             ← Domain entity implementing DDD Aggregate Root
    │   │   │   ├── Enum.java                           ← Enum class forming relationship with Aggregate Root Entity
    │   │   │   ├── Entity.java                         ← Entity class forming relationship with Aggregate Root Entity
    │   │   │   └── ValueObject.java                    ← VO class forming relationship with Aggregate Root Entity
    │   │   ├── infra
    │   │   │   └── {Aggregate name}Controller.java   ← Inbound Adaptor providing RESTful API endpoints
    │   │   ├── handler
    │   │   │   └── EventHandler.java                   ← Event handler class that receives events and proceeds with subsequent logic
    │   │   └── service
    │   │       └── {Aggregate name}Service.java      ← Service class responsible for logic processing in Repository
    │   └── pom.xml
    ├── mall-main/                                      ← Application entry point (main app)
    │   ├── src/
    │   │   ├── main/
    │   │   │   ├── {{options.packagePath}}/MainApplication.java   ← @SpringBootApplication
    │   │   │   └── config/                             ← Global configurations (e.g., CORS, Swagger, Security)
    │   │   └── resources/
    │   │       └── application.yml                     ← Application configuration file
    │   └── pom.xml                                     ← Dependencies specific to mall-main


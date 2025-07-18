Please generate the project based on the following requirements:

1. The {{options.package}} folder must be created, and the package structure and code must be generated under it.

2. Refer to package-structure.mdc and generate the package structure and code in the following order:
Root pom.xml > common-module pom.xml > domain service(domain, infra, service, handler...) > main-module

3. When generating pom files, refer to pom-generation-rules.mdc to identify which type of pom corresponds to the one you are creating before generation.

4. When implementing classes for domain services, generate them based on Metadata information, and do not arbitrarily create data that is not in the Metadata.

5. When implementing classes for domain services, refer to domain-implementation-rules.mdc to identify which type of class corresponds to the one you are creating before generation.

6. After project generation is complete, run mvn install from the Root path, then move to the main-module path and run mvn spring-boot:run.
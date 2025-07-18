The POM files generated in the project should be created based on the following requirements and examples.

1. Root pom.xml

- Include common configurations (e.g., UTF-8...)
- Declare modules to be inherited by sub-modules (domain service modules, execution module, common module)
- Do not declare dependencies separately.

Example

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.3</version>
    <relativePath/> <!-- This is required because spring-boot-starter-parent is an external dependency -->
</parent>

<!-- Common configurations -->
<properties>
    <java.version>17</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>

<!-- Sub-module list -->
<modules>
    <module>common-module</module>
    <module>order-module</module>
    <module>product-module</module>
    <module>delivery-module</module>
    <module>mall-main</module> <!-- Execution module -->
</modules>

---

2. common-module pom.xml
- Inherits from Root pom
- Declare dependencies for common libraries used in domain services (DB, Lombok, Test...)

Example

<parent>
    <groupId>com.example</groupId>
    <artifactId>mall</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>

<artifactId>common-module</artifactId>
<packaging>jar</packaging>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
</build>

---
3. Domain Service-module pom.xml

- Inherits from Root pom
- Declare common module dependency in dependencies
- Add dependencies for libraries used only in this service (all other commonly used dependencies should only be declared in common-module, not in service modules)

Example

<parent>
    <groupId>com.example</groupId>
    <artifactId>mall</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>

<artifactId>order-module</artifactId>
<packaging>jar</packaging>

<dependencies>
    <!-- Common Module -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>common-module</artifactId>
        <version>${project.version}</version>
    </dependency>
    <dependency>
        <!-- Declare libraries used only in order-module if they exist -->
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
</build>

---
4. main-module pom.xml

- Inherits from Root pom
- Declare common-module and all service modules declared in the project in dependencies (because Application.java responsible for execution exists in main-module)

Example


<parent>
    <groupId>com.example</groupId>
    <artifactId>mall</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>

<artifactId>mall-main</artifactId>
<packaging>jar</packaging>

<properties>
    <java.version>17</java.version>
</properties>

<dependencies>
    <!-- Common Module -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>common-module</artifactId>
        <version>${project.version}</version>
    </dependency>

    <!-- Product Module -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>product-module</artifactId>
        <version>${project.version}</version>
    </dependency>

    <!-- Order Module -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>order-module</artifactId>
        <version>${project.version}</version>
    </dependency>

    <!-- Delivery Module -->
    <dependency>
        <groupId>com.example</groupId>
        <artifactId>delivery-module</artifactId>
        <version>${project.version}</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
# Spring
## Spring boot
### Maven
#### Basic setup using parent
```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.0.4.RELEASE</version>
</parent>
```
#### Maven plugin provides extra goals:
- spring-boot:run
- spring-boot:repackage
- spring-boot:start/spring-boot:stop
- spring-boot:build-info

[Plugin link](https://docs.spring.io/spring-boot/docs/2.0.4.RELEASE/maven-plugin/)

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```
#### Starters
[Starters list](https://docs.spring.io/spring-boot/docs/2.0.4.RELEASE/reference/htmlsingle/#using-boot-starter)

Example starter in dependencies
```xml
<dependencies>
	<dependency>
  		<groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
	</dependency>
  <dependency>
  	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
	</dependency>
</dependencies>
```
### Application
#### SpringBootApplication
Can be replaced by @EnableAutoConfiguration and @ComponentScan
#### Main
```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```
#### Logger
```java
private static Logger log = LoggerFactory.getLogger(HelloService.class);
```
#### Build jar
`mvn clean install spring-boot:repackage`
Without clean install may not works!
### Auto restart
Add spring dev tools to dependencies
```xml
<!-- DevTools  - auto restart -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<optional>true</optional>
</dependency>
```
When *build* (CTRL-F9) a project it will be autorestarted.
### Configuartion
Use @Value annotation to set value in bean from file (application.properties) or other sourece.
```java
@Service
public class HelloService {

    private static Logger log = LoggerFactory.getLogger(HelloService.class);

    @Value("${name}")
    private String name;

    public void sayHello(){
        log.info("Hello "+name);
    }
}
```
#### Simple unit test
In unit tests set this field using reflection
```java
@Test
    public void setPropertyFromTest(){
        HelloService helloService = new HelloService();

        ReflectionTestUtils.setField(helloService, "name", "Test");

        helloService.sayHello();
    }
```
#### Spring test
In full spring test using properties file with the same name and path what test class
```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
@TestPropertySource
public class HelloServiceTwoTest {

    @Autowired
    private HelloService helloService;

    @Test
    public void setPropertyFromTest(){
        helloService.sayHello();
    }
}
```
File HelloServiceTwoTest.properties
```java
name=Test property
```


## How to deploy Spring-Boot application?


Basically you’ll need to make four intuitive changes:

- Edit the web entry into your application (```Application.java``` file).
- Exclude Tomcat dependencies of  ```spring-boot-starter-web``` plugin in your ```pom.xml``` file.
- Add **Servlet 3.1** dependencies.
- Change the Maven packaging form **jar** to **war**.

Here is the Application.java class that Spring Boot provides: 

```
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
	
}
```

Following is the modified ```Application.java``` file for deployment which spring-boot provides : 
```
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;

@SpringBootApplication
public class Application extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		// TODO Auto-generated method stub
		return builder.sources(Application.class);
	}

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
	
}
```
Exclude Tomcat dependencies : 
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
            	<exclusion>
            		<groupId>org.springframework.boot</groupId>
            		<artifactId>spring-boot-starter-tomcat</artifactId>
            	</exclusion>
            	<exclusion>
            		<groupId>org.apache.tomcat.embed</groupId>
            		<artifactId>tomcat-embed-core</artifactId>
            	</exclusion>
            	<exclusion>
            		<groupId>org.apache.tomcat.embed</groupId>
            		<artifactId>tomcat-embed-el</artifactId>
            	</exclusion>
            	<exclusion>
            		<groupId>org.apache.tomcat.embed</groupId>
            		<artifactId>tomcat-embed-websocket</artifactId>
            	</exclusion>
            </exclusions>
        </dependency>
```

Since we need Servlet 3.1 we need to add the dependencies here.
```
<dependency>
   <groupId>javax.servlet</groupId>
   <artifactId>javax.servlet-api</artifactId>
   <version>3.1.0</version>
   <scope>provided</scope>
</dependency>
<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>7.0</version>
    <scope>provided</scope>
</dependency>
```
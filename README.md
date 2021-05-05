# ConfigServerCloud
This application contains remote configurations for applications using the Spring Cloud Config package.
The idea is that our programs can go to find their configuration parameters in an external place, in such a way that our applications are easily parameterizable.

## Configuration server
To create the configuration server you will need to include this dependency: 

    <dependency>
    	<groupId>org.springframework.cloud</groupId>
    	<artifactId>spring-cloud-config-server</artifactId>
    </dependency>

and add `@EnableConfigServer` annotation to the main spring class.
The configuration server will use this GitHub repository and its application.properties will contains: 

    server.port=8888  
    spring.application.name=config-server  
    spring.cloud.config.server.git.uri=https://github.com/mdababi1989/ConfigServerCloud

The configuration file must be named after the client that is going to request the data. So if the client is named **courses-service**, we must have a file called **courses-service.properties** in whitch we will put the values in the format:
`key: value` or `key=value`
To retreive the configurations for the profile prod, you would use this url:
 *http://localhost:8888/courses-service/produ*
The default profile will be used  if you do not specify any.
if a client requests a value and that value exists in the requested profile, that value will be returned. Otherwise, the default profile would be searched , returning the assigned value if it had one.

## Configuration client
To create the configuration client you will need to include this dependency: 

    <dependency>
    	<groupId>org.springframework.cloud</groupId>
    	<artifactId>spring-cloud-starter-config</artifactId>
    </dependency>

To specify where the configuration server is, we will add the **bootstrap.properties** file: 

    spring.application.name=courses-service
    spring.cloud.config.uri=http://localhost:8888
    spring.profiles.active=prod (optional)
    management.endpoints.web.exposure.include=refresh
The property **management.endpoints.web.exposure.include** configures the actuator package in such a way that the URL **http://localhost:8080/actuator/refresh** will force to refresh the different properties.
#### Read the configuration

    @Component @ConfigurationProperties("courses")
     public class Configuration {
    	private int minTitleLength;
    	private int maxChapterNumber;	
    }

**courses** is the root of the properties to read. We will read  **courses.minTitleLength** and **courses.maxChapterNumber**.
We can inject the Configuration component in other classes using 

    @Autowired 
    private  Configuration configuration;

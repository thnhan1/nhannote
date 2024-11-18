---
title: "Spring Boot: Quick Review of 50 Commonly - used Annotations. Recommended for Collection!"
source: https://medium.com/@junfeng0828/0c7a5c4ecf51
author:
  - "[[Dylan Smith]]"
published: 2024-11-06
created: 2024-11-18
description: There are countless projects developed based on the Spring Boot platform. Thanks to the large number of annotations provided by Spring Boot for rapid development, it is very simple and can basically…
tags:
  - clippings
  - "#springboot"
  - "#annotation"
  - "#medium"
---
## All Commonly Used Annotations



![Image](https://miro.medium.com/v2/resize:fit:1400/1*lrTGE4A6EZI9_J8ZX6D_Zg.png)

## 1.Components-related Annotations

## @Controller
Used to decorate components in the `controller` layer in MVC
```java
	@Controller
	@RequestMapping("/user/admin")
	class UserAdminController {}
```
## @Service
đánh dấu là service bean cho spring container quản lý.
```java
@Servicepublic class UserService {
	// business login
}
```
## `@Repository`
Used to decorate components in the `dao` layer. Components in the `dao` layer focus on the processing of system data.
```java
@Repository
public interface RoleRepository extends JpaRepository<Role, Long> {    }
```
## `@Component`
Component chung mục đích để tạo ra các bean cho spring quản lý dùng cho DI.

```java
@Componentpublic class DemoHandler {        }
```

## 2\. Annotations related to Bean instances and life cycles

## `@Bean`
Used to decorate methods, indicating that this method will create a Bean instance and be managed by the Spring container. The sample code is as follows:

```java
@Configuration
public class AppConfig {
	@Bean
	public Uploader initFileUploader() { 
	       return new FileUploader(); 
	 }
}
```
## @Scope

Used to declare the scope of a Spring `Bean` instance. The scopes are as follows:

- **singleton**: Singleton pattern. The instance is unique in the Spring container. This is the default instance pattern in Spring.
- **prototype**: Prototype pattern. The instance is recreated every time it is used.
- **request**: The same instance is used in the same request. A new instance is created for different requests.
- **session**: The same instance is used in the same session. A new instance is created for different sessions.
```java
@Configuration
public class RestTemplateConfig {
  @Bean
  @Scope("singleton")
  public RestTemplate restTemplate() {
    return new RestTemplate();
  }
}
```

## @Primary

Khi có nhiều instance của bean. primary giúp đánh dấu cái nào ưu tiên hơn.

```java
@Configuration
@ComponentScan
public class JavaConfig {

  @Bean("b1")
  @Primary
  B b1() {
    return new B();
  }

  @Bean("b2")
  B b2() {
    return new B();
  }
}

```

## @PostConstruct

Used to decorate a method. It is executed after the object instance is created and dependency injection is completed. It can be used for initializing the object instance.

## @PreDestroy

Used to decorate a method. It is executed when the object instance is about to be removed by the Spring container. It can be used for releasing the resources held by the object instance.

```java
public class Demo {

  public Demo() {
    System.out.println("constructor method...");
  }

  public void init() {
    System.out.println("init...");
  }

  @PostConstruct
  public void postConstruct() {
    System.out.println("postConstruct...");
  }

  @PreDestroy
  public void preDestroy() {
    System.out.println("preDestroy...");
  }

  public void destroy() {
    System.out.println("destroy...");
  }
}
```

Output:

```java
constructor method...postConstruct...init...preDestroy...destroy...
```

## 3.Dependency Injection Annotations

## `@Autowired`

Automatically injects dependent objects according to the type of the object. By default, it requires the injected object instance to exist. You can configure `required=false` to inject an object that may not necessarily exist.

```java
@Controller
@RequestMapping("/user")
public class UserController {

  @Autowired
  private UserService userService;

  @Autowired(required = false)
  private UserConfig userConfig;
}

```

## @Resource

By default, it automatically injects dependent objects according to the `name` of the object. If you want to inject according to the type, you can set the property to `type = UmsAdminService.class`.

```java
@Controller
@RequestMapping("/user")
public class UserController {

  @Resource(name = "userServiceImpl")
  private UserService userService;
}
```

## `@Qualifier`

When there are multiple `beans` of the same type, using `@Autowired` to import will result in an error, indicating that the current object is not unique and Spring does not know which dependency to import. At this time, we can use `@Qualifier` for finer-grained control and select one of the candidates. It is generally used in conjunction with `@Autowired`. The example is as follows:

```java
@Autowired
@Qualifier("deptService")
private DeptService deptService;

```

## 4\. SpringMVC-related Annotations

## `@RequestMapping`

Provides routing information and is responsible for mapping from `URL` to specific functions in `Controller`. When used on a method, it can specify the request protocol, such as `GET`, `POST`, `PUT`, `DELETE`, and so on.

## `@RequestBody`

Indicates that the `Content-Type` of the request body must be data in the `application/json` format. After receiving the data, it will automatically bind the data to the `Java` object.

## `@ResponseBody`

Indicates that the return result of this method is directly written into the `HTTP response body`. The format of the returned data is `application/json`.

For example, if the request parameter is in `json` format and the return parameter is also in `json` format, the sample code is as follows:

```java
@Controller
@RequestMapping("api")
public class LoginController {

  @RequestMapping(value = "login", method = RequestMethod.POST)
  @ResponseBody
  public ResponseEntity login(@RequestBody UserLoginDTO request) {
    return new ResponseEntity(HttpStatus.OK);
  }
}
```

## `@RestController`

Like `@Controller`, it is used to annotate controller layer components. The difference is that it is a combination of `@ResponseBody` and `@Controller`.

That is, when `@RestController` is used on a class, it means that for all externally exposed interface methods in the current class, the format of the returned data is `application/json`. The sample code is as follows:

```java
@RestController
@RequestMapping("/api")
public class LoginController {

  @RequestMapping(value = "/login", method = RequestMethod.POST)
  public ResponseEntity login(@RequestBody UserLoginDTO request) {
    return new ResponseEntity(HttpStatus.OK);
  }
}

```

## `@RequestParam`

Used to receive data where the request parameter is in form type. It is usually used in front of the parameters of a method. The sample code is as follows:

```java
@RequestMapping(value = "login", method = RequestMethod.POST)
@ResponseBodypublic
ResponseEntity login(
  @RequestParam(value = "userName", required = true) String userName,
  @RequestParam(value = "userPwd", required = true) String userPwd
) {
  return new ResponseEntity(HttpStatus.OK);
}
```

## `@PathVariable`

Used to obtain parameters in the request path. It is usually used on `restful` style APIs. The sample code is as follows:

```java
@RequestMapping(value = "queryProduct/{id}", method = RequestMethod.POST)
@ResponseBodypublic
ResponseEntity queryProduct(@PathVariable("id") String id) {
  return new ResponseEntity(HttpStatus.OK);
}

```

## `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`

In addition to `@RequestMapping` being able to specify the request method, there are some other annotations that can be used to annotate interface path requests. For example, when `@GetMapping` is used on a method, it means that only the `get` request method is supported. It is equivalent to `@RequestMapping(value="/get", method=RequestMethod.GET)`.

```java
@GetMapping("get")
public ResponseEntity get(){ 
		return new ResponseEntity(HttpStatus.OK);
	}
```

## 5.Configuration-related Annotations

## `@Configuration`

Indicates that a Java-based configuration class is declared. Spring Boot advocates Java-based configuration, which is equivalent to configuring beans in xml before. For example, declare a configuration class `AppConfig`, and then initialize an `Uploader` object.

```java
@Configurationpublic class AppConfig {    @Bean    public Uploader initOSSUploader() {        return new OSSUploader();    }}
```
## `@EnableAutoConfiguration`

`@EnableAutoConfiguration` can help Spring Boot applications load all eligible `@Configuration` configuration classes into the current Spring Boot, create `Beans` corresponding to the configuration classes, and hand over the `Bean` entities to the IoC container for management.

In some scenarios, if we want to avoid the scanning of certain configuration classes (including avoiding configurations under some third-party jars), it can be handled in this way.

```java
@Configuration
@EnableAutoConfiguration(
  exclude = {org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration.class,
  }
)
public class AppConfig {}

```

## `@ComponentScan`

Annotates which classes under which paths need to be scanned by Spring. It is used for automatically discovering and assembling some `Bean` objects. The default configuration is to scan all classes in the current folder and subdirectories. If we want to specify scanning certain package paths, it can be handled like this.

```java
@ComponentScan(basePackages = {"com.xxx.a", "com.xxx.b", "com.xxx.c"})
```

## `@SpringBootApplication`

It is equivalent to using the three annotations `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`. It is usually used on the global startup class. The example is as follows:

```java
@SpringBootApplicationpublic
class PropertyApplication {

  public static void main(String[] args) {
    SpringApplication.run(PropertyApplication.class, args);
  }
}

```

Replacing `@SpringBootApplication` with these three annotations `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` can also start successfully. `@SpringBootApplication` just simplifies these three annotations.

## `@EnableTransactionManagement`

Indicates enabling transaction support, which is equivalent to `<tx:annotation-driven />` in the xml configuration method.

```java
@SpringBootApplication@EnableTransactionManagement\`public class PropertyApplication {    public static void main(String[] args) {        SpringApplication.run(PropertyApplication.class, args);    }}
```

## @ConfigurationProperties

Used for batch injection of external configurations and importing configurations with a specified prefix in the form of an object. For example, here we specify the properties with the prefix `secure.ignored` in `application.yml`:

```java
secure:  ignored:    urls: #Security path whitelist.      - /swagger-ui/      - /swagger-resources/**      - *.html      - *.js      - *.css      - *.png      - /favicon.ico      - /actuator/**
```

Then, by defining a `urls` property in the Java class, the properties in the configuration file can be imported.

```java
@Getter
@Setter
@Configuration
@ConfigurationProperties(prefix = "secure.ignored")
public class IgnoreUrlsConfig {

  private List<String> urls = new ArrayList<>();
}

```

## `@Conditional`

Starting from Spring 4, the `@Conditional` annotation can be used to load `bean` objects conditionally. Currently, in the Spring Boot source code, the `@Condition` annotation has been extensively extended and is used to implement intelligent automatic configuration to meet various usage scenarios. Here are some commonly used annotations:

- `@ConditionalOnBean`: The configuration takes effect when a specific `Bean` exists.
- `@ConditionalOnMissingBean`: The configuration takes effect when a specific `Bean` does not exist.
- `@ConditionalOnClass`: The configuration takes effect when a specified class exists in the `Classpath`.
- `@ConditionalOnMissingClass`: The configuration takes effect when a specified class does not exist in the `Classpath`.
- `@ConditionalOnExpression`: The configuration takes effect when the calculated result of a given `SpEL` expression is `true`.
- `@ConditionalOnProperty`: The configuration takes effect when a specified configuration property has a definite value and matches.

The specific application case is as follows:

```java
@Configurationpublic
class ConditionalConfig {

  @ConditionalOnBean(Test.class)
  @Bean
  public A createA() {
    return new A();
  }

  @ConditionalOnMissingBean(Test.class)
  @Bean
  public B createB() {
    return new B();
  }

  @ConditionalOnClass(Test.class)
  @Bean
  public C createC() {
    return new C();
  }

  @ConditionalOnMissingClass(Test.class)
  @Bean
  public D createD() {
    return new D();
  }

  @ConditionalOnExpression("${enableConfig:false}")
  @Bean
  public E createE() {
    return new E();
  }

  @ConditionalOnProperty(
    prefix = "filter",
    name = "loginFilter",
    havingValue = "true"
  )
  @Bean
  public F createF() {
    return new F();
  }
}

```

## `@value`

In any Spring-managed Bean, you can obtain the property value configured from any source through this annotation. For example, in the `application.properties` file, you define a parameter variable!

```
config.name=Dylan
```

Inside any `bean` container, you can inject parameters through the `@Value` annotation and obtain the value of the parameter variable.

```java
@RestControllerpublic
class HelloController {

  @Value("${config.name}")
  private String configName;

  @GetMapping("config")
  public String config() {
    return JSON.toJSONString(configName);
  }
}

```

## `@ConfigurationProperties`

The practice of using `@Value` to obtain property configuration values in each class as mentioned above is actually not recommended.

In general enterprise project development, such a messy writing method is not used and maintenance is also troublesome. Usually, a Java configuration class is read at one time, and then this class can be directly referenced where it is needed for use, so that it can be accessed multiple times and is convenient for maintenance. The example is as follows:

First, define the parameter variables in the `application.properties` file.

```
config.name=demo_1config.value=demo_value_1
```

Then, create a Java configuration class and inject the parameter variables.

```java
@Component
@ConfigurationProperties(prefix = "config")
public class Config {

  public String name;
  public String value;
}
```

Finally, where it is needed, inject the `Config` object through `ioc`.

## `@PropertySource`

This annotation is used to read our custom configuration files. For example, to import two configuration files, `test.properties` and `bussiness.properties`, the usage is as follows:

```java
@SpringBootApplication
@PropertySource(value = { "test.properties", "bussiness.properties" })
public class PropertyApplication {

  public static void main(String[] args) {
    SpringApplication.run(PropertyApplication.class, args);
  }
}
```

## `@ImportResource`

Used to load xml configuration files. For example, to import a custom `aaa.xml` file, the usage is as follows:

```java
@ImportResource(locations = "classpath:aaa.xml")
@SpringBootApplicationpublic
class PropertyApplication {

  public static void main(String[] args) {
    SpringApplication.run(PropertyApplication.class, args);
  }
}
```

## 6\. JPA-related Annotations

## `@Entity` and `@Table`

Indicate that this is an entity class. These two annotations are generally used together. However, if the table name is the same as the entity class name, `@Table` can be omitted.

## `@Id`

Indicates that this attribute field corresponds to the primary key field in the database table.

## `@Column`

Indicates the column name in the database table corresponding to this attribute field. If the field name is the same as the column name, it can be omitted.

## `@GeneratedValue`

Indicates the generation strategy of the primary key. There are four options as follows:

- **AUTO**: Indicates that it is controlled by the program and is the default option. If not set, this is it.
- **IDENTITY**: Indicates that it is generated by the database and uses database auto-increment. Oracle does not support this method.
- **SEQUENCE**: Indicates that the primary key ID is generated through the database sequence. MySQL does not support this.
- **Table**: Indicates that the primary key is generated by a specific database. This method is beneficial for database migration.

## `@SequenceGeneretor`

Used to define a sequence for generating primary keys. It needs to be used in conjunction with `@GeneratedValue` to be effective. Taking the `TB_ROLE` table as an example, the corresponding annotation configuration is as follows:

```java
@Entity
@Table(name = "TB_ROLE")
@SequenceGenerator(
  name = "id_seq",
  sequenceName = "seq_repair",
  allocationSize = 1
)
public class Role implements Serializable {

  private static final long serialVersionUID = 1L;

  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "id_seq")
  private Long id;

  @Column(nullable = false)
  private String roleName;

  @Column(nullable = false)
  private String roleType;
}
```

## `@Transient`

Indicates that this attribute does not map to the fields of the database table. The ORM framework will ignore this attribute.

```
@Column(nullable = false)@Transientprivate String lastTime;
```

## `@Basic(fetch = FetchType.LAZY)`

Used on certain attributes, it can achieve the effect of lazy loading. That is, when this field is used, this attribute will be loaded. If it is configured as `fetch = FetchType.EAGER`, it means immediate loading, which is also the default loading method!

```
@Column(nullable = false)@Basic(fetch = FetchType.LAZY)private String roleType;
```

## `@JoinColumn`

Used to annotate the fields that represent the relationship between tables. It is usually used in conjunction with `@OneToOne` and `@OneToMany`. For example:

```
@Entity@Table(name = "tb_login_log")public class LoginLog implements Serializable {    @OneToOne    @JoinColumn(name = "user_id")    private User user;        }
```

## `@OneToOne`, `@OneToMany` and `@ManyToOne`

These three annotations are equivalent to the `one-to-one`, `one-to-many`, and `many-to-one` configurations in the hibernate configuration file. For example, in the following customer address table, customer information can be queried through the customer ID.

```
@Entity@Table(name="address")public class AddressEO implements java.io.Serializable {        @ManyToOne(cascade = { CascadeType.ALL })    @JoinColumn(name="customer_id")    private CustomerEO customer;    }
```

## 7\. Annotations related to exception handling

## `@ControllerAdvice` and `@ExceptionHandler`

They are usually used in combination to handle global exceptions. The sample code is as follows:

```java
@Slf4j
@Configuration
@ControllerAdvicepublic
class GlobalExceptionConfig {

  private static final Integer GLOBAL_ERROR_CODE = 500;

  @ExceptionHandler(value = Exception.class)
  @ResponseBody
  public void exceptionHandler(
    HttpServletRequest request,
    HttpServletResponse response,
    Exception e
  ) throws Exception {
    log.error("Uniform exception handler: ", e);
    ResultMsg<Object> resultMsg = new ResultMsg<>();
    resultMsg.setCode(GLOBAL_ERROR_CODE);
    if (e instanceof CommonException) {
      CommonException ex = (CommonException) e;
      if (ex.getErrCode() != 0) {
        resultMsg.setCode(ex.getErrCode());
      }
      resultMsg.setMsg(ex.getErrMsg());
    } else {
      resultMsg.setMsg(CommonErrorMsg.SYSTEM_ERROR.getMessage());
    }
    WebUtil.buildPrintWriter(response, resultMsg);
  }
}
```

## 8.AOP-related Annotations

## @Aspect

Used to define an aspect. An aspect is a combination of advice and a pointcut. It defines when and where to apply the advice functionality.

## @Before

Represents a before advice. The advice method will be executed before the target method is called. The advice describes the work to be done by the aspect and when it is executed.

## @After

Represents an after advice. The advice method will be executed after the target method returns or throws an exception.

## @AfterReturning

Represents a returning advice. The advice method will be executed after the target method returns.

## @AfterThrowing

Represents a throwing advice. The advice method will be executed after the target method throws an exception.

## @Around

Represents an around advice. The advice method will wrap the target method and execute custom behavior before and after the target method is called.

## @Pointcut

Defines a pointcut expression, which defines the scope where the advice functionality is applied.

## @Order

Used to define the execution order of components. In AOP, it refers to the execution order of aspects. A lower value of the `value` attribute indicates a higher priority.

***Example***:

```java
@Aspect
@Component
@Order(1)
public class WebLogAspect {

  private static final Logger LOGGER = LoggerFactory.getLogger(
    WebLogAspect.class
  );

  @Pointcut("execution(public * com.dylan.smith.web.controller.*.*(..))")
  public void webLog() {}

  @Before("webLog()")
  public void doBefore(JoinPoint joinPoint) throws Throwable {}

  @AfterReturning(value = "webLog()", returning = "ret")
  public void doAfterReturning(Object ret) throws Throwable {}

  @Around("webLog()")
  public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
    WebLog webLog = new WebLog();
    Object result = joinPoint.proceed();
    LOGGER.info("{}", JSONUtil.parse(webLog));
    return result;
  }
}

```

## 9.Testing-related Annotations

## @Test

Specifies a method as a test method.

## `@ActiveProfiles`

Generally applied to test classes, it is used to declare the active Spring configuration file. For example, specify the `application-dev.properties` configuration file.

## `@RunWith` and `@SpringBootTest`

Generally applied to test classes, they are used for unit testing. The example is as follows:

```java 
@ActiveProfiles("dev")
@RunWith(SpringRunner.class)
@SpringBootTestpublic
class TestJunit {

  @Test
  public void executeTask() {}
}

```


# Spring Boot Notes

Target: Spring Boot 3.x (Spring Framework 6, Java 17+, `jakarta.*` packages). Version-specific claims are marked. Spring Boot 4 / Spring Framework 7 are not covered in detail here; check the release notes before relying on anything that changed after Boot 3.5.

Example domain used everywhere: `Order`, `Customer`, `OrderService`, `InventoryService` (same as the Kafka notes in [kafka-review.md](../kafka/kafka-review.md)).

## Index

1. [Spring Core](#1-spring-core)
   - 1.1 [IoC container and beans](#11-ioc-container-and-beans)
   - 1.2 [Dependency injection](#12-dependency-injection)
   - 1.3 [Bean lifecycle](#13-bean-lifecycle)
   - 1.4 [Bean scopes](#14-bean-scopes)
   - 1.5 [Stereotype annotations, @Configuration and @Bean](#15-stereotype-annotations-configuration-and-bean)
   - 1.6 [Qualifier, Primary and circular dependencies](#16-qualifier-primary-and-circular-dependencies)
   - 1.7 [Profiles](#17-profiles)
   - 1.8 [Value and ConfigurationProperties](#18-value-and-configurationproperties)
2. [How Spring Boot Works](#2-how-spring-boot-works)
   - 2.1 [Starters](#21-starters)
   - 2.2 [SpringBootApplication and startup](#22-springbootapplication-and-startup)
   - 2.3 [Auto-configuration and conditional beans](#23-auto-configuration-and-conditional-beans)
   - 2.4 [Configuration files and precedence](#24-configuration-files-and-precedence)
   - 2.5 [Embedded Tomcat and DevTools](#25-embedded-tomcat-and-devtools)
   - 2.6 [Fat jar](#26-fat-jar)
   - 2.7 [Logging](#27-logging)
3. [REST Layer](#3-rest-layer)
   - 3.1 [Controllers and request mapping](#31-controllers-and-request-mapping)
   - 3.2 [DTOs vs entities](#32-dtos-vs-entities)
   - 3.3 [Validation](#33-validation)
   - 3.4 [Global exception handling](#34-global-exception-handling)
   - 3.5 [Pagination](#35-pagination)
   - 3.6 [DispatcherServlet request flow](#36-dispatcherservlet-request-flow)
   - 3.7 [Filters vs interceptors](#37-filters-vs-interceptors)
   - 3.8 [Calling other services](#38-calling-other-services)
4. [Data Access](#4-data-access)
   - 4.1 [Entities and relationships](#41-entities-and-relationships)
   - 4.2 [Repositories, derived queries and Query](#42-repositories-derived-queries-and-query)
   - 4.3 [Lazy vs eager loading and the N+1 problem](#43-lazy-vs-eager-loading-and-the-n1-problem)
   - 4.4 [Transactional](#44-transactional)
   - 4.5 [Optimistic locking](#45-optimistic-locking)
   - 4.6 [Connection pool](#46-connection-pool)
   - 4.7 [Flyway and Liquibase](#47-flyway-and-liquibase)
5. [AOP and Proxies](#5-aop-and-proxies)
   - 5.1 [Proxies: JDK vs CGLIB](#51-proxies-jdk-vs-cglib)
   - 5.2 [Aspects](#52-aspects)
   - 5.3 [Caching](#53-caching)
   - 5.4 [Async and scheduling](#54-async-and-scheduling)
6. [Spring Security](#6-spring-security)
   - 6.1 [Filter chain, authentication and authorization](#61-filter-chain-authentication-and-authorization)
   - 6.2 [SecurityFilterChain configuration](#62-securityfilterchain-configuration)
   - 6.3 [Password encoding](#63-password-encoding)
   - 6.4 [JWT flow with a custom filter](#64-jwt-flow-with-a-custom-filter)
   - 6.5 [Method security](#65-method-security)
   - 6.6 [CORS and CSRF](#66-cors-and-csrf)
   - 6.7 [OAuth2 resource server](#67-oauth2-resource-server)
7. [Microservices with Spring](#7-microservices-with-spring)
   - 7.1 [Service discovery with Eureka](#71-service-discovery-with-eureka)
   - 7.2 [API Gateway](#72-api-gateway)
   - 7.3 [Config Server](#73-config-server)
   - 7.4 [Resilience4j](#74-resilience4j)
   - 7.5 [Tracing and correlation ID](#75-tracing-and-correlation-id)
   - 7.6 [Spring Kafka](#76-spring-kafka)
   - 7.7 [Outbox pattern](#77-outbox-pattern)
8. [Testing](#8-testing)
   - 8.1 [Unit tests with JUnit 5 and Mockito](#81-unit-tests-with-junit-5-and-mockito)
   - 8.2 [Slice tests: WebMvcTest and DataJpaTest](#82-slice-tests-webmvctest-and-datajpatest)
   - 8.3 [SpringBootTest and Testcontainers](#83-springboottest-and-testcontainers)
9. [Production Readiness](#9-production-readiness)
   - 9.1 [Actuator and health probes](#91-actuator-and-health-probes)
   - 9.2 [Micrometer, Prometheus and Grafana](#92-micrometer-prometheus-and-grafana)
   - 9.3 [Graceful shutdown](#93-graceful-shutdown)
   - 9.4 [Dockerfile with layered jar](#94-dockerfile-with-layered-jar)
   - 9.5 [Externalized config and secrets](#95-externalized-config-and-secrets)
   - 9.6 [Common production problems](#96-common-production-problems)

---

## 1. Spring Core

### 1.1 IoC container and beans

Inversion of Control (IoC) means the framework creates objects and wires them together instead of your code calling `new`. The container is the `ApplicationContext`. An object it manages is a bean.

- A bean definition says: which class, which scope, which dependencies.
- The container reads definitions (annotations, `@Bean` methods, auto-configuration), creates the objects, injects dependencies, and manages their lifecycle.
- `BeanFactory` is the basic container. `ApplicationContext` adds events, property resolution, i18n and AOP integration. Use `ApplicationContext`.

```java
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        ApplicationContext ctx = SpringApplication.run(OrderApplication.class, args);

        OrderService service = ctx.getBean(OrderService.class);
        // returns the single OrderService bean the container built

        String[] names = ctx.getBeanDefinitionNames();
        // names of every bean definition (yours plus auto-configured ones)
    }
}
```

### 1.2 Dependency injection

Dependency injection (DI) means a class declares what it needs and the container supplies it. There are three styles.

| Style | How | Verdict |
|---|---|---|
| Constructor | Dependencies are constructor parameters | Preferred |
| Setter | Dependencies set through setter methods | Only for optional dependencies |
| Field | `@Autowired` directly on a field | Avoid |

- Constructor injection allows `final` fields (immutable), makes dependencies visible, and lets you build the object in a unit test with `new` and mocks.
- Field injection hides dependencies, needs reflection or Spring to set them, and cannot be `final`.
- With a single constructor, `@Autowired` is not needed (Spring 4.3+).
- Missing dependency fails at startup, not at runtime. This is a feature.

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final InventoryClient inventoryClient;

    public OrderService(
            OrderRepository orderRepository,
            InventoryClient inventoryClient) {
        this.orderRepository = orderRepository;
        this.inventoryClient = inventoryClient;
        // Spring finds both beans by type and passes them in
    }
}
```

```java
@Service
public class BadOrderService {

    @Autowired
    private OrderRepository orderRepository;
    // works, but: not final, cannot be set without Spring, hidden dependency
}
```

### 1.3 Bean lifecycle

The container creates a singleton bean, injects dependencies, runs init callbacks, keeps it for the app lifetime, then runs destroy callbacks on shutdown.

```text
instantiate (constructor)
      |
populate properties (dependency injection)
      |
Aware callbacks (BeanNameAware, ApplicationContextAware)
      |
BeanPostProcessor.postProcessBeforeInitialization
      |
@PostConstruct  ->  InitializingBean.afterPropertiesSet  ->  custom initMethod
      |
BeanPostProcessor.postProcessAfterInitialization   <-- AOP proxies are created here
      |
bean is READY and in use
      |
context closes
      |
@PreDestroy  ->  DisposableBean.destroy  ->  custom destroyMethod
```

- Constructor runs before injection of fields/setters, so do not use injected fields in a constructor. Use `@PostConstruct`.
- `BeanPostProcessor` is the hook Spring itself uses to wrap beans in proxies (see [5.1](#51-proxies-jdk-vs-cglib)).
- `@PreDestroy` is not called for prototype beans.

```java
@Component
public class InventoryCache {

    private Map<String, Integer> stock;

    @PostConstruct
    void load() {
        // runs once, after dependencies are injected
        stock = new ConcurrentHashMap<>();
    }

    @PreDestroy
    void clear() {
        // runs on graceful shutdown
        stock.clear();
    }
}
```

### 1.4 Bean scopes

A scope decides how many instances the container creates and how long they live.

| Scope | Meaning |
|---|---|
| `singleton` (default) | One instance per container, shared by all callers |
| `prototype` | A new instance every time the bean is requested |
| `request` | One instance per HTTP request (web apps) |
| `session` | One instance per HTTP session (web apps) |

- Singleton beans are shared across threads, so they must be stateless or thread-safe. Do not keep per-request data in fields.
- Injecting a prototype into a singleton gives one instance, created once at injection time. Use `ObjectProvider<T>` to get a fresh one each call.
- A request-scoped bean injected into a singleton needs a scoped proxy.

```java
@Component
@Scope("prototype")
public class OrderBuilder {
    // new instance per request to the container
}

@Service
public class CheckoutService {

    private final ObjectProvider<OrderBuilder> builderProvider;

    public CheckoutService(ObjectProvider<OrderBuilder> builderProvider) {
        this.builderProvider = builderProvider;
    }

    public void checkout() {
        OrderBuilder builder = builderProvider.getObject();
        // a fresh OrderBuilder on every call
    }
}

@Component
@Scope(
    value = WebApplicationContext.SCOPE_REQUEST,
    proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestContextData {
    // safe to inject into singletons; the proxy resolves the real bean per request
}
```

### 1.5 Stereotype annotations, @Configuration and @Bean

Stereotype annotations mark a class as a bean found by component scanning. `@Bean` registers a bean from a method, used for classes you do not own.

| Annotation | Use |
|---|---|
| `@Component` | Generic bean |
| `@Service` | Business logic (no extra behaviour, only meaning) |
| `@Repository` | Data access. Also translates persistence exceptions into Spring's `DataAccessException` hierarchy |
| `@Controller` / `@RestController` | Web layer. `@RestController` = `@Controller` + `@ResponseBody` |
| `@Configuration` | Class that declares `@Bean` methods |

- `@Configuration` classes are proxied (CGLIB) so calling one `@Bean` method from another returns the same singleton instead of creating a new object. This is called full mode. `@Configuration(proxyBeanMethods = false)` turns this off for faster startup (lite mode); do it when `@Bean` methods do not call each other.
- Component scanning starts from the package of the `@SpringBootApplication` class and goes down. A bean in a sibling package is not found.

```java
@Configuration(proxyBeanMethods = false)
public class AppConfig {

    @Bean
    public Clock clock() {
        return Clock.systemUTC();
        // bean name is the method name: "clock"
    }

    @Bean
    public RestClient inventoryRestClient(RestClient.Builder builder) {
        return builder
                .baseUrl("http://inventory-service")
                .build();
    }
}
```

### 1.6 Qualifier, Primary and circular dependencies

When two beans match the same type, injection is ambiguous. `@Primary` and `@Qualifier` resolve it. A circular dependency is when A needs B and B needs A.

- `@Primary`: the default choice when several candidates exist.
- `@Qualifier("name")`: pick a specific one at the injection point. Qualifier wins over Primary.
- Spring can match by parameter name as a fallback, but do not rely on it.
- Constructor circular dependency fails at startup with `BeanCurrentlyInCreationException`.
- Since Spring Boot 2.6, circular references are prohibited by default (`spring.main.allow-circular-references=false`).
- Fix by redesign: extract a third class, or publish an event. Quick fixes: `@Lazy` on one injection point, or setter injection (setter injection only works after setting `spring.main.allow-circular-references=true`). Both hide a design problem.

```java
public interface PaymentGateway {
    void charge(Order order);
}

@Service
@Primary
public class StripeGateway implements PaymentGateway {
    // used when nothing else is specified
}

@Service("paypalGateway")
public class PaypalGateway implements PaymentGateway {
}

@Service
public class PaymentService {

    private final PaymentGateway gateway;

    public PaymentService(@Qualifier("paypalGateway") PaymentGateway gateway) {
        this.gateway = gateway;
        // gets PaypalGateway even though StripeGateway is @Primary
    }
}
```

```java
@Service
public class A {

    public A(@Lazy B b) {
        // Spring injects a lazy proxy of B, which breaks the cycle at startup
    }
}
```

### 1.7 Profiles

A profile is a named set of beans and properties that is active only in some environments (dev, test, prod).

- Activate with `spring.profiles.active=prod` (property, env var `SPRING_PROFILES_ACTIVE`, or `--spring.profiles.active=prod`).
- `application-prod.yml` loads on top of `application.yml` when `prod` is active.
- `@Profile` limits a bean or configuration class to given profiles. `@Profile("!prod")` means "not prod".

```java
@Configuration
public class NotificationConfig {

    @Bean
    @Profile("dev")
    public NotificationSender consoleSender() {
        return new ConsoleNotificationSender();
        // dev: prints to the log
    }

    @Bean
    @Profile("prod")
    public NotificationSender emailSender() {
        return new EmailNotificationSender();
        // prod: sends real email
    }
}
```

```yaml
# application.yml
spring:
  profiles:
    active: dev
---
# a document separator starts a profile-specific block in the same file
spring:
  config:
    activate:
      on-profile: prod
server:
  port: 8080
```

### 1.8 Value and ConfigurationProperties

Both read values from configuration. `@Value` injects a single value. `@ConfigurationProperties` binds a group of properties to a typed class.

- `@Value("${key:default}")` supports a default after the colon and SpEL with `#{...}`.
- `@ConfigurationProperties` supports relaxed binding (`order.max-items`, `ORDER_MAXITEMS` and `order.maxItems` all match), nested objects, lists, validation, and IDE metadata. Prefer it for anything beyond one or two values.
- Use `@ConfigurationProperties` on a record for immutable config (constructor binding, Boot 3).

```java
@Service
public class ShippingService {

    @Value("${shipping.free-above:100}")
    private int freeAbove;
    // 100 if the property is missing
}
```

```java
@ConfigurationProperties(prefix = "order")
@Validated
public record OrderProperties(
        @Min(1) int maxItems,
        Duration paymentTimeout,
        List<String> allowedCurrencies) {
}
```

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class OrderApplication {
    // finds @ConfigurationProperties classes; or use @EnableConfigurationProperties(OrderProperties.class)
}
```

```yaml
order:
  max-items: 20
  payment-timeout: 30s
  allowed-currencies:
    - USD
    - EUR
```

---

## 2. How Spring Boot Works

### 2.1 Starters

A starter is a single dependency that pulls in a curated set of libraries for one purpose. The Boot parent (or BOM) manages the versions so they work together.

| Starter | Brings in |
|---|---|
| `spring-boot-starter-web` | Spring MVC, Jackson, embedded Tomcat |
| `spring-boot-starter-data-jpa` | Spring Data JPA, Hibernate, HikariCP |
| `spring-boot-starter-security` | Spring Security |
| `spring-boot-starter-validation` | Hibernate Validator (`jakarta.validation`) |
| `spring-boot-starter-actuator` | Production endpoints and Micrometer |
| `spring-boot-starter-test` | JUnit 5, Mockito, AssertJ, MockMvc |

- You do not write versions for Boot-managed libraries.
- To swap Tomcat for Jetty, exclude `spring-boot-starter-tomcat` and add `spring-boot-starter-jetty`.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.5</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- no version: managed by the parent -->
    </dependency>
</dependencies>
```

### 2.2 SpringBootApplication and startup

`@SpringBootApplication` is three annotations in one. `SpringApplication.run` builds the context, runs auto-configuration, and starts the web server.

- `@SpringBootConfiguration`: marks this class as a configuration class.
- `@ComponentScan`: scans this package and sub-packages for beans.
- `@EnableAutoConfiguration`: turns on auto-configuration (see 2.3).

```text
SpringApplication.run(...)
      |
 1. create Environment (properties, profiles)
 2. create ApplicationContext
 3. load bean definitions (component scan + auto-configuration)
 4. refresh context: create singletons, run BeanPostProcessors
 5. start embedded web server (Tomcat)
 6. run ApplicationRunner / CommandLineRunner beans
 7. publish ApplicationReadyEvent  -> app is ready for traffic
```

```java
@Component
public class StartupCheck implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) {
        // runs once after the context is ready, before the app serves as "ready"
    }
}
```

### 2.3 Auto-configuration and conditional beans

Auto-configuration is a set of configuration classes that create beans for you based on what is on the classpath and in the properties. Your own bean always wins.

- Candidate classes are listed in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` inside each starter jar (Boot 2.7+; older versions used `spring.factories`).
- Each class is guarded by conditions, so it only applies when it makes sense.

| Condition | Applies when |
|---|---|
| `@ConditionalOnClass` | The class is on the classpath |
| `@ConditionalOnMissingClass` | The class is not on the classpath |
| `@ConditionalOnBean` | A bean of that type already exists |
| `@ConditionalOnMissingBean` | No bean of that type exists (lets you override) |
| `@ConditionalOnProperty` | A property has a given value |
| `@ConditionalOnWebApplication` | The app is a web app |

- `DataSourceAutoConfiguration` creates a `DataSource` only if a driver is on the classpath and you did not define your own `DataSource` bean.
- See what applied and why: run with `--debug`, or open the `/actuator/conditions` endpoint.
- Exclude one: `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)`.

```java
@AutoConfiguration
@ConditionalOnClass(PdfWatermarker.class)
@EnableConfigurationProperties(WatermarkProperties.class)
public class WatermarkAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public PdfWatermarker pdfWatermarker(WatermarkProperties props) {
        return new PdfWatermarker(props.text());
        // skipped if the application defines its own PdfWatermarker bean
    }

    @Bean
    @ConditionalOnProperty(
        name = "watermark.audit.enabled",
        havingValue = "true")
    public WatermarkAuditListener auditListener() {
        return new WatermarkAuditListener();
        // only created when watermark.audit.enabled=true
    }
}
```

```text
# src/main/resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
com.example.watermark.WatermarkAutoConfiguration
```

### 2.4 Configuration files and precedence

Properties come from many sources. When the same key is set in more than one place, the higher source wins.

Simplified order, highest wins first (full list is in the Boot reference, "Externalized Configuration"):

```text
1. Command line arguments            --server.port=9090
2. Java system properties            -Dserver.port=9090
3. OS environment variables          SERVER_PORT=9090
4. application-{profile}.yml/properties  outside the jar
5. application-{profile}.yml/properties  inside the jar
6. application.yml/properties        outside the jar
7. application.yml/properties        inside the jar
8. @PropertySource, then default properties
```

- Profile files override the base file.
- Env vars use upper case and underscores: `spring.datasource.url` becomes `SPRING_DATASOURCE_URL`. This is how containers override config.
- Config files can be placed next to the jar, in `./config/`, or imported with `spring.config.import`.
- `.properties` and `.yml` are equivalent. If both exist for the same name, properties wins over yml.

```properties
# application.properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/orders
spring.jpa.hibernate.ddl-auto=validate
```

```yaml
# application.yml (same settings)
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/orders
  jpa:
    hibernate:
      ddl-auto: validate
```

```bash
java -jar order-service.jar --server.port=9090
# command line beats every file: app starts on 9090
```

### 2.5 Embedded Tomcat and DevTools

Boot starts Tomcat inside your app as a normal Java object. You ship a jar, not a WAR to deploy on a server.

- Tomcat runs a pool of request threads (default max 200, `server.tomcat.threads.max`). One thread handles one request at a time (thread-per-request model).
- Common settings: `server.port`, `server.servlet.context-path`, `server.tomcat.threads.max`, `server.tomcat.accept-count` (queue when all threads busy), `server.tomcat.max-connections`.
- Virtual threads (Boot 3.2+, Java 21+): `spring.threads.virtual.enabled=true` makes Tomcat use a virtual thread per request. Helps with blocking I/O; pinning on `synchronized` blocks and small connection pools can still limit throughput. See the virtual threads section in [JAVA.md](../java/JAVA.md#314-virtual-threads).
- DevTools (`spring-boot-devtools`, scope `runtime`/optional) restarts the app when classes change, using two classloaders so only your code reloads. It is disabled automatically when running a packaged jar.

```properties
server.port=8080
server.tomcat.threads.max=200
server.tomcat.accept-count=100
spring.threads.virtual.enabled=true
# Boot 3.2+ on Java 21: request handling on virtual threads
```

### 2.6 Fat jar

A fat (executable) jar contains your classes plus all dependency jars and a launcher, so `java -jar` just works.

- Built by `spring-boot-maven-plugin` (`mvn package`) or the Gradle plugin.
- The `JarLauncher` sets up a classloader that reads nested jars in `BOOT-INF/lib`.
- Layered jars split the jar into layers (dependencies, snapshot dependencies, application) so Docker caches the big, rarely changing layers. See [9.4](#94-dockerfile-with-layered-jar).

```text
order-service.jar
 |- META-INF/MANIFEST.MF        Main-Class: JarLauncher, Start-Class: your main class
 |- org/springframework/boot/loader/...   launcher classes
 |- BOOT-INF/classes/           your compiled code and application.yml
 |- BOOT-INF/lib/               all dependency jars
 |- BOOT-INF/layers.idx         layer order (for Docker)
```

```bash
mvn clean package
java -jar target/order-service-1.0.0.jar
```

### 2.7 Logging

Boot uses SLF4J as the API and Logback as the default implementation. You write to SLF4J, so the backend can change.

- Levels: `TRACE < DEBUG < INFO < WARN < ERROR`. Default root level is `INFO`.
- Set levels per package in properties. Change at runtime with the `/actuator/loggers` endpoint.
- Use placeholders `{}` instead of string concatenation (the message is built only if the level is enabled).
- Never log passwords, tokens or full card numbers.
- Use a correlation ID in the log pattern to follow one request across services (see [7.5](#75-tracing-and-correlation-id)).

```java
@Service
public class OrderService {

    private static final Logger log = LoggerFactory.getLogger(OrderService.class);

    public void place(Order order) {
        log.info("Placing order id={} customer={}", order.getId(), order.getCustomerId());
        try {
            // ...
        } catch (Exception ex) {
            log.error("Order failed id={}", order.getId(), ex);
            // last argument is the exception: stack trace is printed
            throw ex;
        }
    }
}
```

```properties
logging.level.root=INFO
logging.level.com.example.order=DEBUG
logging.level.org.hibernate.SQL=DEBUG
logging.pattern.level=%5p [%X{traceId:-}]
# Boot 3.4+ can also write structured JSON logs: logging.structured.format.console=ecs
```

---

## 3. REST Layer

### 3.1 Controllers and request mapping

A controller receives HTTP requests and returns data. `@RestController` writes the return value as JSON (via Jackson) into the response body.

- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping` map HTTP method and path.
- `@PathVariable`: value from the URL path. `@RequestParam`: query string value. `@RequestBody`: JSON body converted to an object. `@RequestHeader`: header value.
- `ResponseEntity` lets you control status code and headers. `@ResponseStatus` sets a fixed status.
- Keep controllers thin: parse input, call a service, return a DTO.

| Operation | Method | Path | Success status |
|---|---|---|---|
| Create order | POST | `/api/orders` | 201 Created + `Location` header |
| Get order | GET | `/api/orders/{id}` | 200 OK |
| Update order | PUT | `/api/orders/{id}` | 200 OK |
| Delete order | DELETE | `/api/orders/{id}` | 204 No Content |
| Not found / invalid input | any | any | 404 / 400 |

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @GetMapping("/{id}")
    public OrderResponse get(@PathVariable Long id) {
        return orderService.find(id);
        // 200 with JSON body
    }

    @GetMapping
    public List<OrderResponse> byStatus(
            @RequestParam(defaultValue = "NEW") OrderStatus status) {
        return orderService.findByStatus(status);
        // GET /api/orders?status=PAID
    }

    @PostMapping
    public ResponseEntity<OrderResponse> create(
            @Valid @RequestBody CreateOrderRequest request) {
        OrderResponse created = orderService.create(request);
        URI location = URI.create("/api/orders/" + created.id());
        return ResponseEntity
                .created(location)
                .body(created);
        // 201 + Location: /api/orders/42
    }

    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable Long id) {
        orderService.delete(id);
        // 204, empty body
    }
}
```

### 3.2 DTOs vs entities

An entity maps to a database table. A DTO (Data Transfer Object) is the shape of data in the API. Keep them separate.

- Returning entities leaks internal columns (password hashes), couples the API to the schema, and triggers lazy-loading errors during JSON serialization.
- Request and response DTOs can differ, and the API can stay stable while the schema changes.
- Java records are a good fit for DTOs (immutable, short).
- Mapping: by hand, or MapStruct (generates mapping code at compile time).

```java
public record CreateOrderRequest(
        @NotNull Long customerId,
        @NotEmpty List<OrderItemRequest> items) {
}

public record OrderResponse(
        Long id,
        Long customerId,
        BigDecimal total,
        OrderStatus status) {

    public static OrderResponse from(Order order) {
        return new OrderResponse(
                order.getId(),
                order.getCustomer().getId(),
                order.getTotal(),
                order.getStatus());
    }
}
```

### 3.3 Validation

Bean Validation (`jakarta.validation`, implemented by Hibernate Validator) checks input with annotations. Needs `spring-boot-starter-validation`.

- `@Valid` on a `@RequestBody` validates the object. Failure throws `MethodArgumentNotValidException` (HTTP 400).
- `@Validated` on the class lets you validate `@PathVariable` / `@RequestParam` values. Failure throws `ConstraintViolationException` (in Spring 6.1+ built-in method validation raises `HandlerMethodValidationException` instead; behaviour depends on version).
- Common annotations: `@NotNull`, `@NotBlank`, `@Size`, `@Min`, `@Max`, `@Email`, `@Pattern`, `@Positive`.
- Custom rule: write an annotation plus a `ConstraintValidator`.

```java
public record OrderItemRequest(
        @NotBlank String sku,
        @Positive int quantity) {
}

public record CreateOrderRequest(
        @NotNull Long customerId,
        @NotEmpty List<@Valid OrderItemRequest> items,
        @Size(max = 200) String note) {
    // @Valid inside the list validates each item too
}
```

```java
@RestController
@Validated
public class CustomerController {

    @GetMapping("/api/customers/{id}")
    public CustomerResponse get(@PathVariable @Positive Long id) {
        // id <= 0 is rejected before this method body runs
        return null;
    }
}
```

### 3.4 Global exception handling

One class turns exceptions from every controller into consistent error responses. `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`.

- `@ExceptionHandler` picks the handler by exception type (most specific wins).
- `ProblemDetail` (Spring 6, Boot 3) is the standard RFC 7807 / RFC 9457 error format: `type`, `title`, `status`, `detail`, `instance`, plus custom fields.
- Setting `spring.mvc.problemdetails.enabled=true` makes Spring's own MVC exceptions return `ProblemDetail`.
- Do not return stack traces to clients. Log them on the server.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(OrderNotFoundException.class)
    public ProblemDetail handleNotFound(OrderNotFoundException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(
                HttpStatus.NOT_FOUND,
                ex.getMessage());
        problem.setTitle("Order not found");
        return problem;
        // {"title":"Order not found","status":404,"detail":"Order 42 not found"}
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ProblemDetail handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult()
                .getFieldErrors()
                .forEach(error -> {
                    errors.put(error.getField(), error.getDefaultMessage());
                });
        ProblemDetail problem = ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);
        problem.setTitle("Validation failed");
        problem.setProperty("errors", errors);
        return problem;
    }

    @ExceptionHandler(Exception.class)
    public ProblemDetail handleOther(Exception ex) {
        log.error("Unhandled error", ex);
        return ProblemDetail.forStatusAndDetail(
                HttpStatus.INTERNAL_SERVER_ERROR,
                "Unexpected error");
        // no internal details leak to the client
    }
}
```

### 3.5 Pagination

Pagination returns a slice of a large result instead of everything. Spring Data gives `Pageable` (page number, size, sort) and `Page` (content plus totals).

- Request: `GET /api/orders?page=0&size=20&sort=createdAt,desc`. Page numbers start at 0.
- `Page` runs an extra `count(*)` query. `Slice` skips the count and only knows if a next page exists. Use `Slice` for infinite scroll.
- Offset pagination gets slow on deep pages (database still scans skipped rows). For huge tables use keyset pagination (`where id > :lastId order by id limit 20`).
- Set a maximum page size (`spring.data.web.pageable.max-page-size`, default 2000).
- Return a DTO page, not entities.

```java
@GetMapping
public Page<OrderResponse> list(
        @PageableDefault(size = 20, sort = "createdAt", direction = Sort.Direction.DESC)
        Pageable pageable) {
    return orderRepository
            .findAll(pageable)
            .map(OrderResponse::from);
    // JSON has content[], totalElements, totalPages, number, size
}
```

### 3.6 DispatcherServlet request flow

`DispatcherServlet` is the single front controller of Spring MVC. Every request goes through it, and it delegates to the right controller.

```text
HTTP request
    |
Tomcat thread
    |
Servlet filters (Security filter chain, CORS, logging)        <-- servlet container level
    |
DispatcherServlet
    |
HandlerMapping           finds the controller method for the URL + HTTP method
    |
HandlerInterceptor.preHandle                                  <-- Spring MVC level
    |
Argument resolvers       @PathVariable, @RequestBody (Jackson converts JSON), @Valid
    |
Controller method  ->  Service  ->  Repository
    |
Return value handlers    object -> JSON via HttpMessageConverter (Jackson)
    |
HandlerInterceptor.postHandle / afterCompletion
    |
Exceptions anywhere above -> HandlerExceptionResolver -> @ControllerAdvice
    |
HTTP response
```

- `HandlerMapping`: maps request to handler. `HandlerAdapter`: calls the handler. `HttpMessageConverter`: converts between Java objects and JSON.

### 3.7 Filters vs interceptors

Both run code around a request. A filter is part of the Servlet API and sees every request. An interceptor is part of Spring MVC and only sees requests that reach `DispatcherServlet`.

| | Filter | HandlerInterceptor |
|---|---|---|
| Belongs to | Servlet container | Spring MVC |
| Runs | Before `DispatcherServlet` | After `DispatcherServlet` picks a handler |
| Knows which controller method | No | Yes (the handler) |
| Can modify request/response body | Yes (with wrappers) | Hard |
| Typical use | Security, JWT, correlation ID, CORS, request logging | Auth checks per handler, timing, locale |

```java
@Component
public class CorrelationIdFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain chain) throws ServletException, IOException {

        String id = request.getHeader("X-Correlation-Id");
        if (id == null) {
            id = UUID.randomUUID().toString();
        }
        MDC.put("correlationId", id);
        response.setHeader("X-Correlation-Id", id);
        try {
            chain.doFilter(request, response);
            // continue to the next filter / DispatcherServlet
        } finally {
            MDC.clear();
            // always clean up: threads are reused
        }
    }
}
```

```java
@Component
public class TimingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler) {
        request.setAttribute("start", System.nanoTime());
        return true;
        // false would stop the request here
    }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final TimingInterceptor timingInterceptor;

    public WebConfig(TimingInterceptor timingInterceptor) {
        this.timingInterceptor = timingInterceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry
                .addInterceptor(timingInterceptor)
                .addPathPatterns("/api/**");
    }
}
```

### 3.8 Calling other services

Spring has several HTTP clients. Pick based on the Spring version and whether the call is blocking.

| Client | What it is | Status |
|---|---|---|
| `RestClient` | Fluent blocking client | Newest blocking client, Spring 6.1 / Boot 3.2+. Use for new code |
| `WebClient` | Fluent non-blocking (reactive) client from WebFlux | Active. Needed for reactive code; also usable blocking with `.block()` |
| `RestTemplate` | Older blocking template | Maintenance mode, not deprecated in 6.x. Still common in existing code |
| OpenFeign | Declarative client: you write an interface, Spring Cloud generates the implementation | Spring Cloud. Good with Eureka and load balancing |

- Always set connect and read timeouts. A call with no timeout can hold a Tomcat thread forever.
- Combine with a circuit breaker and retry (see [7.4](#74-resilience4j)).
- Using the service name (`http://inventory-service`) with a `@LoadBalanced` builder resolves instances through Eureka.

```java
@Configuration
public class ClientConfig {

    @Bean
    public RestClient inventoryClient(RestClient.Builder builder) {
        JdkClientHttpRequestFactory factory = new JdkClientHttpRequestFactory();
        factory.setReadTimeout(Duration.ofSeconds(3));
        return builder
                .baseUrl("http://inventory-service")
                .requestFactory(factory)
                .build();
    }
}

@Component
public class InventoryClient {

    private final RestClient restClient;

    public InventoryClient(RestClient inventoryClient) {
        this.restClient = inventoryClient;
    }

    public StockResponse getStock(String sku) {
        return restClient
                .get()
                .uri("/api/stock/{sku}", sku)
                .retrieve()
                .onStatus(HttpStatusCode::is4xxClientError, (req, res) -> {
                    throw new StockNotFoundException(sku);
                })
                .body(StockResponse.class);
        // blocks until the response is read and converted
    }
}
```

```java
// WebClient (non-blocking)
Mono<StockResponse> stock = webClient
        .get()
        .uri("/api/stock/{sku}", sku)
        .retrieve()
        .bodyToMono(StockResponse.class);
// nothing is sent until someone subscribes to the Mono
```

```java
// OpenFeign (needs @EnableFeignClients on a configuration class)
@FeignClient(
        name = "inventory-service",
        fallback = InventoryFallback.class)
public interface InventoryFeignClient {

    @GetMapping("/api/stock/{sku}")
    StockResponse getStock(@PathVariable("sku") String sku);
    // Spring Cloud builds the implementation; "inventory-service" is resolved via the discovery client
}
```

---

## 4. Data Access

### 4.1 Entities and relationships

An entity is a Java class mapped to a table by JPA (Jakarta Persistence). Hibernate is the JPA implementation Boot uses (Hibernate 6 in Boot 3).

- `@Entity`, `@Id`, `@GeneratedValue`, `@Column`, `@Enumerated(EnumType.STRING)` (never ORDINAL, it breaks when enum order changes).
- Relationships: `@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`.
- The owning side holds the foreign key (the `@ManyToOne` side). The inverse side uses `mappedBy`.
- Prefer `@ManyToOne` and `@OneToMany(mappedBy=...)` over `@ManyToMany` (an explicit join entity is more flexible).
- Implement `equals`/`hashCode` carefully (use id or a business key, not all fields). Do not use Lombok `@Data` on entities.
- Entities need a no-arg constructor (can be `protected`).
- `ddl-auto=update` is for experiments. Use `validate` plus migrations in production.

```java
@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_id", nullable = false)
    private Customer customer;
    // owning side: the orders table has the customer_id column

    @OneToMany(
            mappedBy = "order",
            cascade = CascadeType.ALL,
            orphanRemoval = true)
    private List<OrderItem> items = new ArrayList<>();
    // inverse side: no column here

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

    @Version
    private Long version;

    protected Order() {
    }

    public void addItem(OrderItem item) {
        items.add(item);
        item.setOrder(this);
        // keep both sides of the relationship in sync
    }
}
```

### 4.2 Repositories, derived queries and Query

A Spring Data repository is an interface. Spring generates the implementation at startup. Method names or annotations define the query.

- `JpaRepository<Order, Long>` gives `save`, `findById`, `findAll`, `deleteById`, paging and sorting.
- Derived query: Spring parses the method name (`findByStatusAndCustomerId`).
- `@Query` takes JPQL (queries entities) or native SQL (`nativeQuery = true`). Always use named parameters (`:status`), never string concatenation (SQL injection).
- `@Modifying` is needed for update/delete queries.
- Projections (interfaces or records) fetch only some columns.

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    List<Order> findByStatusAndCustomerId(OrderStatus status, Long customerId);
    // select ... where status = ? and customer_id = ?

    boolean existsByCustomerIdAndStatus(Long customerId, OrderStatus status);

    @Query("""
            select o
            from Order o
            where o.total > :min
              and o.status = :status
            """)
    List<Order> findBigOrders(
            @Param("min") BigDecimal min,
            @Param("status") OrderStatus status);

    @Modifying
    @Query("update Order o set o.status = :status where o.id = :id")
    int updateStatus(
            @Param("id") Long id,
            @Param("status") OrderStatus status);
    // returns number of rows changed; must run inside a transaction

    Page<OrderSummary> findByCustomerId(Long customerId, Pageable pageable);
}

public interface OrderSummary {
    Long getId();
    BigDecimal getTotal();
    // interface projection: only these columns are selected
}
```

### 4.3 Lazy vs eager loading and the N+1 problem

Lazy loading fetches related data only when you access it. Eager loading fetches it together with the parent. The N+1 problem is 1 query for N parents plus N more queries for their children.

- Defaults: `@ManyToOne` and `@OneToOne` are EAGER. `@OneToMany` and `@ManyToMany` are LAZY. Set `@ManyToOne(fetch = LAZY)` explicitly.
- Lazy relations are loaded through a proxy. Touching one outside a session throws `LazyInitializationException`. Fix with a fetch join or DTO query, not by making everything eager.
- `spring.jpa.open-in-view` (default true) keeps the session open until the view is rendered, which hides the exception but holds a DB connection per request. Many teams set it to `false`.

N+1 example:

```java
List<Order> orders = orderRepository.findAll();
// query 1: select * from orders

for (Order order : orders) {
    int count = order.getItems().size();
    // one extra query per order: select * from order_item where order_id = ?
    // 100 orders = 101 queries
}
```

Fixes:

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
            select distinct o
            from Order o
            join fetch o.items
            where o.status = :status
            """)
    List<Order> findWithItems(@Param("status") OrderStatus status);
    // fetch join: one query with a join

    @EntityGraph(attributePaths = {"items", "customer"})
    List<Order> findByStatus(OrderStatus status);
    // same idea without writing JPQL
}
```

```java
@OneToMany(mappedBy = "order")
@BatchSize(size = 50)
private List<OrderItem> items;
// loads children for up to 50 parents in one "where order_id in (...)" query
```

- Do not fetch-join two collections at once (cartesian product, `MultipleBagFetchException` for Lists).
- Fetch join plus pagination makes Hibernate page in memory (Hibernate 6 warning HHH90003004: "firstResult/maxResults specified with collection fetch; applying in memory"). Page the parent ids first, or use `@BatchSize`.
- Detect N+1: `spring.jpa.show-sql=true` or `logging.level.org.hibernate.SQL=DEBUG`, Hibernate statistics, or a test that counts queries.

### 4.4 Transactional

A transaction makes several database operations succeed together or fail together. `@Transactional` starts one around a method and commits or rolls back for you.

How it works: Spring wraps the bean in a proxy. The proxy begins the transaction, calls your method, then commits or rolls back.

```text
caller
  |
  v
[ Proxy of OrderService ]
  |  1. get a connection, begin transaction
  |  2. call the real method  --->  [ real OrderService.place() ]
  |                                       |  repository calls share the same transaction
  |  3. method returns normally  -> commit
  |     method throws RuntimeException/Error -> rollback
  v
caller gets result or exception
```

Rules:

- Rollback happens on unchecked exceptions (`RuntimeException`, `Error`) by default. A checked exception does NOT roll back unless you set `rollbackFor`.
- Self-invocation trap: calling a `@Transactional` method from another method in the same class (`this.save()`) skips the proxy, so there is no transaction. Fix: move the method to another bean, or inject the bean into itself, or use `TransactionTemplate`.
- Only public methods through the proxy are advised. `private`, `final` or `static` methods are not (CGLIB cannot override them).
- Swallowing an exception in a `catch` means the proxy never sees it, so it commits.
- `readOnly = true` is a hint: Hibernate skips dirty checking and some drivers route to a read replica.
- Put `@Transactional` on the service layer, not the controller or repository.
- A long transaction holds a connection. Do not call slow HTTP services inside one.

Propagation (what happens when a transactional method calls another):

| Propagation | Behaviour |
|---|---|
| `REQUIRED` (default) | Join the existing transaction, or start one if none |
| `REQUIRES_NEW` | Suspend the current one and start a new independent transaction |
| `SUPPORTS` | Join if one exists, otherwise run without |
| `NOT_SUPPORTED` | Run without a transaction, suspending any existing one |
| `MANDATORY` | Must run inside one, else throw |
| `NEVER` | Must run without one, else throw |
| `NESTED` | Savepoint inside the current transaction (needs JDBC savepoint support) |

Isolation (how much concurrent transactions can see of each other):

| Level | Prevents |
|---|---|
| `READ_UNCOMMITTED` | Nothing (dirty reads possible) |
| `READ_COMMITTED` | Dirty reads |
| `REPEATABLE_READ` | Dirty reads and non-repeatable reads. MySQL InnoDB default |
| `SERIALIZABLE` | Also phantom reads. Slowest |

`DEFAULT` uses the database default.

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final AuditService auditService;

    public OrderService(
            OrderRepository orderRepository,
            AuditService auditService) {
        this.orderRepository = orderRepository;
        this.auditService = auditService;
    }

    @Transactional(rollbackFor = Exception.class)
    public Order place(CreateOrderRequest request) throws PaymentException {
        Order order = orderRepository.save(new Order());
        auditService.record("ORDER_PLACED");
        // REQUIRES_NEW inside: audit row is committed even if place() rolls back later
        charge(order);
        // a checked PaymentException also rolls back because of rollbackFor
        return order;
    }

    @Transactional(readOnly = true)
    public Order find(Long id) {
        return orderRepository
                .findById(id)
                .orElseThrow(() -> new OrderNotFoundException(id));
    }

    public void process() throws PaymentException {
        this.place(null);
        // WRONG: self-invocation, bypasses the proxy, no transaction is started here
    }
}

@Service
public class AuditService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void record(String event) {
        // own transaction, commits independently
    }
}
```

Enable SQL transaction logs while learning: `logging.level.org.springframework.transaction.interceptor=TRACE`.

### 4.5 Optimistic locking

Optimistic locking detects that someone else changed a row between your read and your write, without holding database locks.

- Add a `@Version` field. Hibernate adds `where version = ?` to every update and increments it.
- If zero rows are updated, Hibernate throws `ObjectOptimisticLockingFailureException`. Retry the operation or tell the user to reload.
- Good when conflicts are rare. For hot rows (stock count) consider pessimistic locking (`@Lock(LockModeType.PESSIMISTIC_WRITE)`, which uses `select ... for update`) or an atomic `update stock set qty = qty - :n where qty >= :n`.

```java
@Entity
public class Stock {

    @Id
    private String sku;

    private int quantity;

    @Version
    private Long version;
    // update stock set quantity=?, version=2 where sku=? and version=1
}
```

```java
@Retryable(
        retryFor = ObjectOptimisticLockingFailureException.class,
        maxAttempts = 3)
@Transactional
public void reserve(String sku, int qty) {
    Stock stock = stockRepository.findById(sku).orElseThrow();
    stock.setQuantity(stock.getQuantity() - qty);
    // a concurrent change causes an exception at commit; the retry re-reads fresh data
    // @Retryable needs spring-retry + @EnableRetry; put retry outside the transaction
}
```

### 4.6 Connection pool

A pool keeps database connections open and reuses them, because opening one per request is slow. Boot uses HikariCP by default.

- A request that needs a connection waits when the pool is empty, up to `connection-timeout`, then fails with a timeout error.
- Pool size should be small. A common starting point is 10 to 20 per instance. More connections than the database can serve make things slower. Total across all instances must fit the database `max_connections`.
- A leak (connection not returned) usually comes from a long transaction or from `open-in-view` with slow rendering. `leak-detection-threshold` logs it.

```properties
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=10
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.leak-detection-threshold=20000
# max-lifetime must be shorter than the database or proxy idle timeout
```

### 4.7 Flyway and Liquibase

A migration tool applies versioned SQL scripts to the database in order and records what ran. The schema is then reproducible in every environment.

- Flyway: plain SQL files named `V1__create_orders.sql`. Located in `db/migration`. Boot runs them at startup when `flyway-core` (and `flyway-mysql` for MySQL 8 with Flyway 10+) is on the classpath.
- Liquibase: changelogs in XML/YAML/SQL with more features (rollback, contexts).
- Never edit an applied migration (checksum mismatch). Add a new version.
- Set `spring.jpa.hibernate.ddl-auto=validate` so Hibernate checks the schema matches entities.
- Zero-downtime deploys: make schema changes backward compatible (add column first, then deploy code, then drop old column later).

```sql
-- src/main/resources/db/migration/V1__create_orders.sql
CREATE TABLE orders (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    customer_id BIGINT NOT NULL,
    status VARCHAR(20) NOT NULL,
    total DECIMAL(12, 2) NOT NULL,
    version BIGINT NOT NULL DEFAULT 0
);
```

```properties
spring.flyway.enabled=true
spring.flyway.baseline-on-migrate=true
spring.jpa.hibernate.ddl-auto=validate
```

---

## 5. AOP and Proxies

### 5.1 Proxies: JDK vs CGLIB

A proxy is an object that stands in front of your real bean and has the same type. Callers use the proxy, and the proxy adds behaviour (transaction, cache, security) before and after delegating to the real object.

- Aspect-Oriented Programming (AOP) adds cross-cutting behaviour (logging, transactions, security) without editing each method.
- Spring creates the proxy in `BeanPostProcessor.postProcessAfterInitialization` (see the lifecycle in [1.3](#13-bean-lifecycle)). The container holds the proxy under the bean name.

| | JDK dynamic proxy | CGLIB proxy |
|---|---|---|
| How | `java.lang.reflect.Proxy` implements the bean's interfaces | Generates a subclass of the bean class |
| Needs | The bean implements an interface | A non-final class and non-final methods |
| Injection by | Interface type only | Class type works |
| Boot default | Not used | CGLIB (`spring.aop.proxy-target-class=true`) |

- `final` classes/methods and `private` methods cannot be advised with CGLIB.
- Inside the real object, `this` is the target, not the proxy. That is why self-invocation skips advice.

```text
caller ---> [ Proxy ] ---> before advice ---> [ Real OrderService ] ---> after advice
                                                    ^
                                      this.otherMethod() calls here directly,
                                      NOT back through the proxy (advice skipped)
```

```java
@Service
public class OrderService {

    @Transactional
    public void a() {
    }

    public void b() {
        a();
        // self-invocation: goes straight to the real object, no transaction advice
    }
}
// In the container: orderService bean = OrderService$$SpringCGLIB$$0 (a subclass)
```

### 5.2 Aspects

An aspect is a class that holds advice (the code to run) and a pointcut (which methods to match). Needs `spring-boot-starter-aop`.

- Advice types: `@Before`, `@AfterReturning`, `@AfterThrowing`, `@After`, `@Around` (wraps the call, can change the result, must call `proceed()`).
- Pointcut expressions: `execution(...)` by method signature, `within(...)` by type, `@annotation(...)` by annotation.
- Spring AOP only works on Spring beans and only on method calls through the proxy. AspectJ weaving can do more but is rarely needed.
- `@Transactional`, `@Async`, `@Cacheable`, `@PreAuthorize` and `@Retryable` are all proxy-based advice, so they share the self-invocation limitation.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Timed {
}

@Aspect
@Component
public class TimingAspect {

    private static final Logger log = LoggerFactory.getLogger(TimingAspect.class);

    @Around("@annotation(com.example.order.Timed)")
    public Object time(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.nanoTime();
        try {
            return joinPoint.proceed();
            // calls the real method
        } finally {
            long ms = (System.nanoTime() - start) / 1_000_000;
            log.info("{} took {} ms", joinPoint.getSignature(), ms);
        }
    }
}
```

### 5.3 Caching

Caching stores the result of a method so the next call with the same arguments skips the work. `@Cacheable` is applied by a proxy.

- Enable with `@EnableCaching`. A cache provider stores the data: simple in-memory map (default), Caffeine (local), or Redis (shared between instances).
- `@Cacheable`: return cached value if present, else call and store. `@CachePut`: always call and update. `@CacheEvict`: remove entry.
- Key defaults to the method arguments. Set `key = "#id"` (SpEL) when needed.
- Set a TTL (time to live) or data stays stale forever. Redis TTL is configured in `RedisCacheConfiguration`.
- Cached objects in Redis must be serializable (JSON serializer recommended).
- Local caches are per instance, so instances can disagree. Use Redis when that matters.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

```properties
spring.cache.type=redis
spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.cache.redis.time-to-live=10m
```

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public RedisCacheConfiguration cacheConfiguration() {
        return RedisCacheConfiguration
                .defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(10))
                .serializeValuesWith(
                        RedisSerializationContext.SerializationPair.fromSerializer(
                                new GenericJackson2JsonRedisSerializer()));
    }
}

@Service
public class CustomerService {

    @Cacheable(cacheNames = "customers", key = "#id")
    public CustomerResponse find(Long id) {
        // first call hits the database; later calls with the same id return from Redis
        return null;
    }

    @CacheEvict(cacheNames = "customers", key = "#id")
    public void update(Long id, UpdateCustomerRequest request) {
        // removes the cached entry so the next read reloads it
    }
}
```

### 5.4 Async and scheduling

`@Async` runs a method on another thread. `@Scheduled` runs a method on a timer. Both are proxy-based.

- `@Async` needs `@EnableAsync`. The method must be called from another bean (self-invocation does not work). Return `void` or `CompletableFuture<T>`.
- Without configuration Boot uses a `ThreadPoolTaskExecutor` (auto-configured, `spring.task.execution.*`). Define your own pool with bounded queue size. An unbounded queue can hide overload.
- Exceptions in `void` async methods are lost unless you register an `AsyncUncaughtExceptionHandler`.
- `@Transactional` context and `SecurityContext`/`MDC` do not move to the new thread automatically.
- `@Scheduled` needs `@EnableScheduling`. The default scheduler has ONE thread, so a slow job blocks others (`spring.task.scheduling.pool.size`).
- With multiple instances, every instance runs the job. Use ShedLock or a database lock to run it once.

```java
@Configuration
@EnableAsync
@EnableScheduling
public class AsyncConfig {

    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(4);
        executor.setMaxPoolSize(8);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}

@Service
public class NotificationService {

    @Async
    public CompletableFuture<Void> sendEmail(Long orderId) {
        // runs on an "async-" thread, caller continues immediately
        return CompletableFuture.completedFuture(null);
    }
}

@Component
public class OrderCleanupJob {

    @Scheduled(cron = "0 0 2 * * *")
    public void cancelUnpaidOrders() {
        // every day at 02:00 server time
    }

    @Scheduled(fixedDelay = 60_000)
    public void poll() {
        // 60 s after the previous run FINISHED (fixedRate = start to start)
    }
}
```

---
## 6. Spring Security

### 6.1 Filter chain, authentication and authorization

Authentication answers "who are you?". Authorization answers "what are you allowed to do?". Spring Security does both in a chain of servlet filters that runs before `DispatcherServlet`.

- A single servlet filter, `DelegatingFilterProxy` / `FilterChainProxy`, holds one or more `SecurityFilterChain`s and picks the first one that matches the request.
- Each security filter does one job. Order matters.
- `SecurityContextHolder` stores the current `Authentication` (principal, credentials, authorities) in a `ThreadLocal`, so any code on the request thread can read it.
- `AuthenticationManager` (usually `ProviderManager`) asks `AuthenticationProvider`s to verify credentials. `UserDetailsService` loads the user from your database. `PasswordEncoder` checks the password.

```text
HTTP request
    |
FilterChainProxy -> picks matching SecurityFilterChain
    |
 1. SecurityContextHolderFilter       load/clear the SecurityContext
 2. HeaderWriterFilter                security response headers
 3. CorsFilter                        CORS rules
 4. CsrfFilter                        CSRF check (state-changing requests)
 5. LogoutFilter
 6. [ your JwtAuthenticationFilter ]  added before UsernamePasswordAuthenticationFilter
 7. UsernamePasswordAuthenticationFilter   form login (not used in a JWT API)
 8. ExceptionTranslationFilter        turns exceptions into 401 / 403
 9. AuthorizationFilter               checks the rules (hasRole, authenticated)
    |
DispatcherServlet -> Controller
```

- 401 Unauthorized = not authenticated (no or bad credentials). 403 Forbidden = authenticated but not allowed.
- The exact list of filters depends on configuration. Turn on `logging.level.org.springframework.security=DEBUG` to see the real chain.

### 6.2 SecurityFilterChain configuration

In Spring Security 6 you declare a `SecurityFilterChain` bean. `WebSecurityConfigurerAdapter` is removed. Use the lambda DSL.

- `authorizeHttpRequests` defines rules. First matching rule wins, so put specific rules first and `anyRequest()` last.
- A stateless REST API uses `SessionCreationPolicy.STATELESS` (no HTTP session) and usually disables CSRF (see [6.6](#66-cors-and-csrf)).
- `requestMatchers` replaced `antMatchers` and `mvcMatchers`.

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtFilter;

    public SecurityConfig(JwtAuthenticationFilter jwtFilter) {
        this.jwtFilter = jwtFilter;
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .csrf(csrf -> csrf.disable())
                .sessionManagement(session -> {
                    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS);
                })
                .authorizeHttpRequests(auth -> {
                    auth.requestMatchers("/api/auth/**", "/actuator/health/**").permitAll();
                    auth.requestMatchers(HttpMethod.DELETE, "/api/orders/**").hasRole("ADMIN");
                    auth.requestMatchers("/api/**").authenticated();
                    auth.anyRequest().denyAll();
                })
                .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }

    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

### 6.3 Password encoding

Passwords must be stored as a slow, salted hash, never plain text and never a fast hash like MD5 or SHA-256 alone.

- A salt is random data added to each password before hashing, so equal passwords get different hashes.
- BCrypt is the common default (cost factor makes it slow on purpose). Argon2 and SCrypt are also available.
- `PasswordEncoder.encode` hashes. `matches(raw, hash)` verifies. You cannot decrypt a hash.
- `DelegatingPasswordEncoder` (`PasswordEncoderFactories.createDelegatingPasswordEncoder()`) stores a prefix like `{bcrypt}` so algorithms can be upgraded later.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

```java
@Service
public class CustomerUserDetailsService implements UserDetailsService {

    private final CustomerRepository customerRepository;

    public CustomerUserDetailsService(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String email) {
        Customer customer = customerRepository
                .findByEmail(email)
                .orElseThrow(() -> new UsernameNotFoundException(email));
        return User
                .withUsername(customer.getEmail())
                .password(customer.getPasswordHash())
                .roles(customer.getRole())
                .build();
        // roles("ADMIN") becomes authority ROLE_ADMIN
    }
}

String hash = passwordEncoder.encode("secret");
// different on every call because of the random salt
boolean ok = passwordEncoder.matches("secret", hash);
// true
```

### 6.4 JWT flow with a custom filter

A JWT (JSON Web Token) is a signed string `header.payload.signature` that carries claims such as user id and roles. The server verifies the signature instead of looking up a session, so it is stateless.

- Payload is Base64URL-encoded, NOT encrypted. Do not put secrets in it.
- Sign with HS256 (shared secret) or RS256 (private key signs, public key verifies; better when many services verify).
- Keep access tokens short (5 to 15 minutes). Use a refresh token to get new ones. Logout and revocation need a deny-list or short expiry, because a valid JWT cannot be "cancelled" by itself.
- Always check signature, expiry (`exp`), and ideally issuer/audience.
- Store the secret outside the code (env var, secret manager).

```text
LOGIN
 Client --POST /api/auth/login {email, password}--> AuthController
        AuthenticationManager.authenticate(...)  (UserDetailsService + PasswordEncoder)
 Client <--{ "accessToken": "eyJ..." }------------ JwtService.generate(user)

LATER REQUESTS
 Client --GET /api/orders  Authorization: Bearer eyJ...--> JwtAuthenticationFilter
        1. read header, strip "Bearer "
        2. verify signature + expiry
        3. build Authentication, put in SecurityContextHolder
        4. chain.doFilter(...)  -->  AuthorizationFilter  -->  Controller
 If token missing or invalid: SecurityContext stays empty -> 401
```

Token creation and parsing with the jjwt library (version 0.12 API; method names differ in older versions):

```java
@Service
public class JwtService {

    private final SecretKey key;

    public JwtService(@Value("${app.jwt.secret}") String secret) {
        this.key = Keys.hmacShaKeyFor(Decoders.BASE64.decode(secret));
    }

    public String generate(UserDetails user) {
        Instant now = Instant.now();
        return Jwts
                .builder()
                .subject(user.getUsername())
                .claim("roles", user.getAuthorities()
                        .stream()
                        .map(GrantedAuthority::getAuthority)
                        .toList())
                .issuedAt(Date.from(now))
                .expiration(Date.from(now.plus(15, ChronoUnit.MINUTES)))
                .signWith(key)
                .compact();
    }

    public Claims parse(String token) {
        return Jwts
                .parser()
                .verifyWith(key)
                .build()
                .parseSignedClaims(token)
                .getPayload();
        // throws JwtException if signature is wrong or the token is expired
    }
}
```

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthenticationFilter(
            JwtService jwtService,
            UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain chain) throws ServletException, IOException {

        String header = request.getHeader(HttpHeaders.AUTHORIZATION);
        if (header == null || !header.startsWith("Bearer ")) {
            chain.doFilter(request, response);
            return;
            // no token: continue unauthenticated; later rules decide (401 if protected)
        }

        try {
            String token = header.substring(7);
            Claims claims = jwtService.parse(token);
            UserDetails user = userDetailsService.loadUserByUsername(claims.getSubject());

            UsernamePasswordAuthenticationToken authentication =
                    new UsernamePasswordAuthenticationToken(
                            user,
                            null,
                            user.getAuthorities());
            SecurityContextHolder
                    .getContext()
                    .setAuthentication(authentication);
        } catch (JwtException ex) {
            SecurityContextHolder.clearContext();
            // invalid or expired token: stay unauthenticated
        }
        chain.doFilter(request, response);
    }
}
```

### 6.5 Method security

Method security checks permissions on a service method, using annotations. It is proxy-based (see [5.1](#51-proxies-jdk-vs-cglib)).

- Enable with `@EnableMethodSecurity` (Spring Security 6; replaces `@EnableGlobalMethodSecurity`). `@PreAuthorize` and `@PostAuthorize` are on by default.
- `@PreAuthorize` checks before the call. `@PostAuthorize` checks after, and can use `returnObject`.
- Expressions: `hasRole('ADMIN')`, `hasAuthority('order:read')`, `#id == authentication.principal.id`, and bean calls `@orderSecurity.isOwner(#id, authentication)`.
- Self-invocation skips the check, same as `@Transactional`.
- Failure throws `AccessDeniedException`, which becomes 403.

```java
@Service
public class OrderService {

    @PreAuthorize("hasRole('ADMIN')")
    public void deleteAll() {
    }

    @PreAuthorize("hasRole('ADMIN') or @orderSecurity.isOwner(#id, authentication)")
    public OrderResponse find(Long id) {
        return null;
        // allowed for admins or the customer who owns the order
    }

    @PostAuthorize("returnObject.customerId == authentication.principal.id")
    public OrderResponse findUnchecked(Long id) {
        return null;
        // runs first, then the result is checked
    }
}
```

### 6.6 CORS and CSRF

CORS controls which browser origins may call your API. CSRF is an attack where another site makes the browser send a request using the user's cookie.

- CORS (Cross-Origin Resource Sharing): browsers block a page on `https://shop.com` from reading responses from `https://api.shop.com` unless the API sends `Access-Control-Allow-Origin`. It is enforced by the browser only, not a security control for servers or `curl`.
- Preflight: for non-simple requests (JSON body, custom headers, PUT/DELETE) the browser first sends an `OPTIONS` request. Security must allow it, so enable CORS in the security chain, otherwise the `CorsFilter` is skipped and preflight gets 401.
- Do not use `allowedOrigins("*")` together with credentials.
- CSRF: needed when authentication is automatic through cookies (session login). A stateless API that reads a token from the `Authorization` header is not vulnerable to CSRF, so disabling it is acceptable. If you store a JWT in a cookie, keep CSRF protection or use `SameSite` cookies.

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("https://shop.example.com"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    config.setMaxAge(3600L);
    // browser may cache the preflight answer for one hour

    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/api/**", config);
    return source;
}

// inside the SecurityFilterChain:
// http.cors(cors -> cors.configurationSource(corsConfigurationSource()));
```

### 6.7 OAuth2 resource server

A resource server is an API that accepts access tokens issued by a separate authorization server (Keycloak, Okta, Spring Authorization Server). It validates tokens and does not issue them.

- Add `spring-boot-starter-oauth2-resource-server`. Spring validates JWT signatures using the issuer's public keys (JWKS endpoint) and builds the `Authentication` for you. This replaces a hand-written JWT filter when tokens come from an identity provider.
- Roles are usually in a claim (`scope`, or `realm_access.roles` in Keycloak). Map them with a `JwtAuthenticationConverter`.
- Flows (grant types): Authorization Code with PKCE for user login in browsers/apps, Client Credentials for service-to-service. Implicit and Password grants are deprecated.

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth.example.com/realms/shop
          # Spring downloads the public keys from the issuer's discovery document
```

```java
http
        .authorizeHttpRequests(auth -> {
            auth.requestMatchers("/api/**").authenticated();
        })
        .oauth2ResourceServer(oauth2 -> {
            oauth2.jwt(Customizer.withDefaults());
        });
```

---

## 7. Microservices with Spring

### 7.1 Service discovery with Eureka

Service discovery lets services find each other by name instead of fixed host and port, because instances start, stop and change IP addresses (containers, scaling).

- Eureka Server is a registry. Each service registers itself (name, host, port) and sends heartbeats (default every 30 s). Instances with no heartbeat are evicted.
- Clients fetch and cache the registry, so a call still works for a while if Eureka is down.
- Spring Cloud LoadBalancer picks one instance for each call (client-side load balancing, default round robin).
- In Kubernetes, the platform already provides discovery (Service DNS), so Eureka is often unnecessary there.
- Spring Cloud versions must match the Boot version (release trains, such as 2023.0.x with Boot 3.2/3.3). Check the compatibility table.

```java
@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServerApplication {
}
```

```yaml
# order-service application.yml (Eureka client)
spring:
  application:
    name: order-service
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    prefer-ip-address: true
```

```java
@Bean
@LoadBalanced
public RestClient.Builder loadBalancedBuilder() {
    return RestClient.builder();
    // "http://inventory-service/..." is resolved to a real instance on each call
}
```

### 7.2 API Gateway

A gateway is the single entry point in front of all services. It routes requests and handles cross-cutting concerns once.

- Spring Cloud Gateway is built on WebFlux (reactive, non-blocking), so do not add blocking code in its filters. Note: newer Spring Cloud release trains split Gateway into server artifacts (WebFlux and MVC variants); check the artifact names for your version. In the 2025.0 train the starter is `spring-cloud-starter-gateway-server-webflux` and the properties move to `spring.cloud.gateway.server.webflux.*` (the YAML below uses the older `spring.cloud.gateway.*` names).
- Route = id + target URI + predicates (match conditions) + filters (modify request/response).
- Typical gateway jobs: routing, JWT validation, rate limiting, CORS, correlation ID, request logging, retry.
- `lb://order-service` means "resolve through discovery and load balance".

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=0
            - name: CircuitBreaker
              args:
                name: orderCb
                fallbackUri: forward:/fallback/orders
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20
```

```java
@Component
public class CorrelationIdGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String id = UUID.randomUUID().toString();
        ServerHttpRequest request = exchange
                .getRequest()
                .mutate()
                .header("X-Correlation-Id", id)
                .build();
        return chain.filter(exchange.mutate().request(request).build());
    }

    @Override
    public int getOrder() {
        return -1;
        // run early
    }
}
```

### 7.3 Config Server

Spring Cloud Config Server serves configuration files from a Git repository to all services, so config is centralized and versioned.

- Client imports it with `spring.config.import=configserver:http://config-server:8888`.
- Files are named `{application}-{profile}.yml` in the Git repo.
- `@RefreshScope` beans reload after `POST /actuator/refresh`. Spring Cloud Bus can broadcast the refresh to all instances.
- Secrets should not sit in plain text in Git. Use encryption support, Vault, or the platform's secret store.
- In Kubernetes, ConfigMaps and Secrets often replace Config Server (see [9.5](#95-externalized-config-and-secrets)).

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {
}
```

```yaml
# config server
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/example/shop-config
```

```yaml
# client (order-service)
spring:
  application:
    name: order-service
  config:
    import: "optional:configserver:http://localhost:8888"
```

### 7.4 Resilience4j

Resilience4j adds fault tolerance around calls to other services, so one slow or failing service does not take the whole system down. Use it through `resilience4j-spring-boot3` and annotations.

- Circuit breaker: stops calling a failing service for a while and fails fast.
- Retry: repeats a failed call. Only retry idempotent calls (same result if repeated), use backoff, and a limited count.
- Rate limiter: limits calls per time window.
- Time limiter: cancels a call that takes too long (for async return types).
- Bulkhead: limits concurrent calls so one dependency cannot use all threads.
- Always have a fallback that returns a degraded result or a clear error.
- Annotation order (outermost first) by default: Retry, CircuitBreaker, RateLimiter, TimeLimiter, Bulkhead.

Circuit breaker states:

```text
              failure rate >= threshold (for example 50% of last 10 calls)
  CLOSED  ------------------------------------------------>  OPEN
 (calls pass,                                          (calls rejected at once
  results counted)                                      with CallNotPermittedException)
     ^                                                          |
     |                                                          | wait duration passes (e.g. 10 s)
     | trial calls succeed                                      v
     +---------------------------------------------------  HALF_OPEN
                                                           (a few trial calls allowed)
                          trial calls fail  ->  back to OPEN
```

```yaml
resilience4j:
  circuitbreaker:
    instances:
      inventory:
        sliding-window-size: 10
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10s
        permitted-number-of-calls-in-half-open-state: 3
  retry:
    instances:
      inventory:
        max-attempts: 3
        wait-duration: 500ms
        enable-exponential-backoff: true
        retry-exceptions:
          - java.io.IOException
  ratelimiter:
    instances:
      inventory:
        limit-for-period: 50
        limit-refresh-period: 1s
        timeout-duration: 0
  timelimiter:
    instances:
      inventory:
        timeout-duration: 2s
```

```java
@Service
public class InventoryService {

    private final InventoryClient inventoryClient;

    public InventoryService(InventoryClient inventoryClient) {
        this.inventoryClient = inventoryClient;
    }

    @CircuitBreaker(name = "inventory", fallbackMethod = "stockFallback")
    @Retry(name = "inventory")
    public StockResponse checkStock(String sku) {
        return inventoryClient.getStock(sku);
    }

    private StockResponse stockFallback(String sku, Throwable ex) {
        return StockResponse.unknown(sku);
        // same parameters plus the exception; used when the call fails or the circuit is open
    }
}
```

### 7.5 Tracing and correlation ID

A correlation ID (or trace ID) is one identifier attached to a request and passed to every service it touches, so logs from all services can be joined.

- Micrometer Tracing (Boot 3, replaces Spring Cloud Sleuth) creates a `traceId` and `spanId` and propagates them in HTTP headers (W3C `traceparent` by default) and Kafka headers.
- A bridge (OpenTelemetry or Brave) and an exporter send spans to a backend (Zipkin, Jaeger, Tempo).
- Trace IDs are put in the MDC (Mapped Diagnostic Context, a per-thread map that the log pattern can print).
- Use the auto-configured `RestClient.Builder`, `RestTemplateBuilder` and `WebClient.Builder`, otherwise headers are not propagated.
- Async threads and Kafka consumers need context propagation (Boot enables observation for Kafka with `spring.kafka.template.observation-enabled` and `spring.kafka.listener.observation-enabled`).

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-otel</artifactId>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-exporter-otlp</artifactId>
</dependency>
```

```properties
management.tracing.sampling.probability=1.0
# 1.0 samples every request; use a lower value in production
logging.pattern.level=%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]
```

If you do not use tracing, the `CorrelationIdFilter` in [3.7](#37-filters-vs-interceptors) does the manual version: read or create `X-Correlation-Id`, put it in the MDC, and forward the header on outgoing calls.

### 7.6 Spring Kafka

`spring-kafka` wraps the Kafka client. `KafkaTemplate` produces messages, `@KafkaListener` consumes them. For the Kafka concepts themselves (topics, partitions, consumer groups, offsets, acks) see [kafka-review.md](../kafka/kafka-review.md).

- Producer: set `acks=all` and `enable.idempotence=true` for no loss and no duplicates from retries. The message key decides the partition, so use `orderId` as the key to keep per-order ordering.
- Consumer: `group-id` defines the consumer group. Concurrency (`concurrency`) is the number of consumer threads, and it cannot usefully exceed the partition count.
- Delivery is at-least-once, so the same message can arrive twice. Consumers must be idempotent (for example, store processed event ids or use a unique constraint).
- Acknowledgement: Boot default is `BATCH` (offsets committed after a poll batch is processed). `AckMode.MANUAL` gives explicit control.
- Error handling: `DefaultErrorHandler` retries with a back-off, then calls a recoverer. `DeadLetterPublishingRecoverer` sends the failed record to a dead-letter topic (DLT, named `<topic>.DLT` by default).
- Without handling, one bad ("poison") message can block a partition forever. Separate non-retryable errors (bad JSON, validation) from retryable ones (temporary DB failure).
- `@RetryableTopic` offers non-blocking retries through retry topics, so a failing message does not block the partition.

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      acks: all
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      properties:
        enable.idempotence: true
    consumer:
      group-id: inventory-service
      auto-offset-reset: earliest
      enable-auto-commit: false
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.ErrorHandlingDeserializer
      properties:
        spring.deserializer.value.delegate.class: org.springframework.kafka.support.serializer.JsonDeserializer
        spring.json.trusted.packages: com.example.events
```

```java
@Service
public class OrderEventPublisher {

    private final KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;

    public OrderEventPublisher(KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void publish(OrderCreatedEvent event) {
        kafkaTemplate
                .send("order-events", event.orderId().toString(), event)
                .whenComplete((result, ex) -> {
                    if (ex != null) {
                        // send failed after the producer's own retries: log, alert, or store for resend
                    }
                });
        // send() is asynchronous: it returns a CompletableFuture, the call does not wait for the broker
    }
}
```

```java
@Component
public class OrderEventListener {

    private final StockService stockService;

    public OrderEventListener(StockService stockService) {
        this.stockService = stockService;
    }

    @KafkaListener(
            topics = "order-events",
            groupId = "inventory-service",
            concurrency = "3")
    public void onOrderCreated(OrderCreatedEvent event) {
        stockService.reserve(event);
        // must be idempotent: the same event may be delivered again
    }
}
```

```java
@Configuration
public class KafkaErrorConfig {

    @Bean
    public DefaultErrorHandler errorHandler(KafkaTemplate<Object, Object> template) {
        DeadLetterPublishingRecoverer recoverer = new DeadLetterPublishingRecoverer(template);
        // failed record goes to "order-events.DLT"

        DefaultErrorHandler handler = new DefaultErrorHandler(
                recoverer,
                new FixedBackOff(1000L, 3L));
        // retry 3 times, 1 second apart, then publish to the DLT

        handler.addNotRetryableExceptions(IllegalArgumentException.class);
        // do not retry errors that retrying cannot fix
        return handler;
        // Boot applies a single CommonErrorHandler bean to the default listener container factory
    }
}
```

### 7.7 Outbox pattern

The outbox pattern solves the dual write problem: saving to the database and publishing to Kafka are two separate systems, so one can succeed while the other fails.

- Save the business change AND an "event" row in the same database transaction (the outbox table). Either both commit or neither does.
- A separate process reads the outbox table and publishes to Kafka, then marks the row sent. Options: a polling job (`@Scheduled`), or Change Data Capture (CDC) with Debezium reading the database log.
- Delivery becomes at-least-once, so consumers must be idempotent.
- `@TransactionalEventListener(phase = AFTER_COMMIT)` publishes after commit but can still lose the message if the app crashes right after commit. The outbox does not have this gap.

```text
 OrderService.place()  --- one DB transaction --->  orders table        (new order)
                                                    outbox table        (event: ORDER_CREATED, status NEW)

 Outbox relay (poller or Debezium)  --reads NEW rows-->  Kafka topic order-events
                                    --marks row SENT-->  outbox table
```

```java
@Transactional
public Order place(CreateOrderRequest request) {
    Order order = orderRepository.save(Order.from(request));
    outboxRepository.save(new OutboxEvent(
            "order-events",
            order.getId().toString(),
            toJson(new OrderCreatedEvent(order.getId()))));
    // both rows are committed together
    return order;
}

@Scheduled(fixedDelay = 2000)
@Transactional
public void relay() {
    List<OutboxEvent> events = outboxRepository.findTop100ByStatusOrderByIdAsc("NEW");
    for (OutboxEvent event : events) {
        kafkaTemplate.send(event.getTopic(), event.getKey(), event.getPayload());
        event.markSent();
        // simple version; real code waits for the send result before marking
    }
}
```

---
## 8. Testing

### 8.1 Unit tests with JUnit 5 and Mockito

A unit test checks one class in isolation. Its dependencies are replaced by mocks (fake objects you control), and no Spring context is started, so it runs in milliseconds.

- This is why constructor injection matters: you create the class with `new` and pass mocks.
- `@ExtendWith(MockitoExtension.class)` enables `@Mock` and `@InjectMocks`.
- `when(...).thenReturn(...)` stubs a call. `verify(...)` checks a call happened. `ArgumentCaptor` captures what was passed.
- Use AssertJ (`assertThat`) for readable assertions. Mockito strict stubs fail the test on unused stubbing.

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private InventoryClient inventoryClient;

    @InjectMocks
    private OrderService orderService;

    @Test
    void createOrder_whenStockAvailable_savesOrder() {
        when(inventoryClient.getStock("SKU-1"))
                .thenReturn(new StockResponse("SKU-1", 10));
        when(orderRepository.save(any(Order.class)))
                .thenAnswer(invocation -> invocation.getArgument(0));

        OrderResponse response = orderService.create(sampleRequest());

        assertThat(response.status()).isEqualTo(OrderStatus.NEW);
        verify(orderRepository).save(any(Order.class));
    }

    @Test
    void createOrder_whenStockMissing_throws() {
        when(inventoryClient.getStock("SKU-1"))
                .thenReturn(new StockResponse("SKU-1", 0));

        assertThatThrownBy(() -> orderService.create(sampleRequest()))
                .isInstanceOf(OutOfStockException.class);
        verifyNoInteractions(orderRepository);
        // nothing was saved
    }
}
```

### 8.2 Slice tests: WebMvcTest and DataJpaTest

A slice test starts only the part of the Spring context for one layer. It is faster than loading the whole application.

| Annotation | Loads | Does not load |
|---|---|---|
| `@WebMvcTest(OrderController.class)` | Controllers, advice, filters, Jackson, MockMvc | `@Service`, `@Repository` (mock them) |
| `@DataJpaTest` | Entities, repositories, Hibernate, an embedded DB by default | Controllers, services |
| `@JsonTest` | Jackson serialization | Everything else |

- `MockMvc` sends fake HTTP requests through `DispatcherServlet` without starting a real server. It checks status, headers and JSON.
- `@MockBean` creates a Mockito mock inside the Spring context. In Boot 3.4+ use `@MockitoBean` (Spring Framework 6.2); `@MockBean` is deprecated from 3.4 (verify for your version).
- `@WebMvcTest` includes Spring Security. Use `@WithMockUser` or `.with(jwt())` / `.with(csrf())` from `spring-security-test`.
- `@DataJpaTest` rolls back each test. To test against real MySQL instead of H2 use Testcontainers with `@AutoConfigureTestDatabase(replace = NONE)`. H2 behaviour can differ from MySQL.

```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockitoBean
    private OrderService orderService;

    @Test
    @WithMockUser
    void getOrder_returnsOrder() throws Exception {
        when(orderService.find(1L))
                .thenReturn(new OrderResponse(1L, 5L, BigDecimal.TEN, OrderStatus.NEW));

        mockMvc
                .perform(get("/api/orders/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(1))
                .andExpect(jsonPath("$.status").value("NEW"));
    }

    @Test
    @WithMockUser
    void createOrder_withEmptyItems_returns400() throws Exception {
        String body = """
                {"customerId": 5, "items": []}
                """;

        mockMvc
                .perform(post("/api/orders")
                        .with(csrf())
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(body))
                .andExpect(status().isBadRequest());
        // @NotEmpty on items rejected the request before the service was called
    }
}
```

```java
@DataJpaTest
class OrderRepositoryTest {

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private TestEntityManager entityManager;

    @Test
    void findByStatus_returnsMatchingOrders() {
        entityManager.persist(newOrder(OrderStatus.PAID));
        entityManager.persist(newOrder(OrderStatus.NEW));
        entityManager.flush();

        List<Order> paid = orderRepository.findByStatus(OrderStatus.PAID);

        assertThat(paid).hasSize(1);
    }
}
```

### 8.3 SpringBootTest and Testcontainers

`@SpringBootTest` starts the full application context. Use it for a few integration tests that cover the real wiring. Testcontainers starts real services (MySQL, Kafka, Redis) in Docker for the test.

- `webEnvironment = RANDOM_PORT` starts the real web server on a random port. Call it with `TestRestTemplate` or `RestClient`/`WebTestClient`.
- Testcontainers needs Docker available on the machine and in CI.
- `@ServiceConnection` (Boot 3.1+) wires the container's URL, user and password into the app automatically. Before 3.1 use `@DynamicPropertySource`.
- Declare the container `static` so it is shared by all tests in the class and starts once.
- Test pyramid: many unit tests, some slice tests, few `@SpringBootTest` tests (they are slow).
- Keep tests independent: do not rely on test order or shared database state.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
class OrderIntegrationTest {

    @Container
    @ServiceConnection
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.4");

    @Container
    @ServiceConnection
    static KafkaContainer kafka = new KafkaContainer(
            DockerImageName.parse("confluentinc/cp-kafka:7.6.1"));

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void createOrder_persistsAndReturns201() {
        CreateOrderRequest request = sampleRequest();

        ResponseEntity<OrderResponse> response = restTemplate.postForEntity(
                "/api/orders",
                request,
                OrderResponse.class);

        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody().id()).isNotNull();
    }
}
```

```java
// alternative to @ServiceConnection (the only option before Boot 3.1)
@DynamicPropertySource
static void properties(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", mysql::getJdbcUrl);
    registry.add("spring.datasource.username", mysql::getUsername);
    registry.add("spring.datasource.password", mysql::getPassword);
}
```

---

## 9. Production Readiness

### 9.1 Actuator and health probes

Actuator (`spring-boot-starter-actuator`) adds HTTP endpoints that show the health and internals of a running app.

| Endpoint | Shows |
|---|---|
| `/actuator/health` | Up or down, with component details if allowed |
| `/actuator/info` | App info (version, git, build) |
| `/actuator/metrics` | Micrometer metrics (JVM, HTTP, pool) |
| `/actuator/prometheus` | Same metrics in Prometheus text format |
| `/actuator/loggers` | View and change log levels at runtime |
| `/actuator/env`, `/configprops`, `/beans`, `/conditions`, `/mappings` | Configuration and wiring (sensitive) |
| `/actuator/threaddump`, `/heapdump` | Diagnostics (very sensitive) |

- Only `health` is exposed over HTTP by default. Expose more explicitly, and protect them with security or a separate management port.
- Health groups for Kubernetes: `/actuator/health/liveness` (is the process alive; restart if not) and `/actuator/health/readiness` (can it take traffic; remove from the Service if not). Enabled automatically on Kubernetes, or set `management.endpoint.health.probes.enabled=true`.
- Do not put external dependencies (database, Kafka) in the liveness group. If the database is down, restarting the app does not help and causes restart storms. Dependencies belong in readiness.
- Add a custom check by implementing `HealthIndicator`.

```properties
management.endpoints.web.exposure.include=health,info,metrics,prometheus
management.endpoint.health.show-details=when-authorized
management.endpoint.health.probes.enabled=true
management.server.port=9091
# actuator on a separate port that is not exposed publicly
```

```java
@Component
public class WatermarkEngineHealthIndicator implements HealthIndicator {

    private final PdfWatermarker watermarker;

    public WatermarkEngineHealthIndicator(PdfWatermarker watermarker) {
        this.watermarker = watermarker;
    }

    @Override
    public Health health() {
        if (watermarker.isReady()) {
            return Health.up().build();
        }
        return Health
                .down()
                .withDetail("reason", "font files missing")
                .build();
    }
}
```

```yaml
# Kubernetes probes for the container
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 9091
  initialDelaySeconds: 30
readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 9091
```

### 9.2 Micrometer, Prometheus and Grafana

Micrometer is the metrics API in Spring (like SLF4J for metrics). It records counters, gauges and timers and exposes them for a monitoring system. Prometheus scrapes them, Grafana draws dashboards.

```text
Spring Boot app (Micrometer)  --exposes-->  /actuator/prometheus
                                                   ^
                                                   | scrape every 15 s (Prometheus pulls)
                                              Prometheus  (stores time series)
                                                   ^
                                                   | PromQL queries
                                                Grafana  (dashboards, alerts)
```

- Needs `micrometer-registry-prometheus` plus Actuator.
- Metric types: Counter (only goes up, e.g. orders created), Gauge (current value, e.g. queue size), Timer (duration and count), DistributionSummary (sizes).
- Built-in metrics: JVM memory/GC/threads, `http.server.requests`, Hikari pool, Tomcat, Kafka client.
- Tags (labels) add dimensions, such as `status` or `uri`. Never use unbounded values (user id, order id) as tags: too many time series (high cardinality) can break Prometheus.
- Add `management.metrics.tags.application=${spring.application.name}` so every metric is labelled with the service.
- Useful alerts: error rate (5xx), p95/p99 latency, JVM heap usage, GC pause, Hikari pending connections, Kafka consumer lag, circuit breaker state.

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
    <scope>runtime</scope>
</dependency>
```

```java
@Service
public class WatermarkService {

    private final Counter watermarked;
    private final Timer watermarkTimer;

    public WatermarkService(MeterRegistry registry) {
        this.watermarked = Counter
                .builder("pdf.watermark.total")
                .description("PDFs watermarked")
                .tag("type", "invoice")
                .register(registry);
        this.watermarkTimer = Timer
                .builder("pdf.watermark.duration")
                .publishPercentileHistogram()
                .register(registry);
    }

    public byte[] apply(byte[] pdf) {
        return watermarkTimer.record(() -> {
            byte[] result = doWatermark(pdf);
            watermarked.increment();
            return result;
        });
        // exposed as pdf_watermark_total and pdf_watermark_duration_seconds_* in Prometheus format
    }
}
```

```yaml
# prometheus.yml
scrape_configs:
  - job_name: order-service
    metrics_path: /actuator/prometheus
    scrape_interval: 15s
    static_configs:
      - targets: ["order-service:9091"]
```

```text
# Example PromQL used in Grafana panels
rate(http_server_requests_seconds_count{application="order-service", status=~"5.."}[5m])
histogram_quantile(0.95, sum(rate(http_server_requests_seconds_bucket[5m])) by (le, uri))
hikaricp_connections_pending
```

### 9.3 Graceful shutdown

Graceful shutdown lets in-flight requests finish before the app stops, so a deploy or scale-down does not cut users off.

- Enable with `server.shutdown=graceful`. The web server stops accepting new requests, waits for active ones up to `spring.lifecycle.timeout-per-shutdown-phase` (default 30 s), then the context closes. Graceful shutdown has been the default since Boot 3.4 (earlier versions: `immediate`). Set it explicitly if you support older versions.
- Kubernetes sends `SIGTERM`, and in parallel removes the pod from the Service endpoints. There is a small window where traffic can still arrive, so add a short `preStop` sleep. `terminationGracePeriodSeconds` must be longer than the shutdown timeout.
- Kafka listeners stop and commit offsets on shutdown. Make sure long processing fits within the timeout.
- Use `SmartLifecycle` or `@PreDestroy` to flush and close resources.

```properties
server.shutdown=graceful
spring.lifecycle.timeout-per-shutdown-phase=30s
```

```yaml
spec:
  terminationGracePeriodSeconds: 45
  containers:
    - name: order-service
      lifecycle:
        preStop:
          exec:
            command: ["sh", "-c", "sleep 5"]
```

```text
SIGTERM received
   |-- readiness goes OUT_OF_SERVICE, new requests no longer accepted
   |-- in-flight requests finish (up to the timeout)
   |-- @PreDestroy callbacks, Kafka consumers stop, connection pool closes
   +-- JVM exits
```

### 9.4 Dockerfile with layered jar

A Docker image is built in layers, and unchanged layers are cached. A fat jar changes on every build, so a single `COPY app.jar` re-uploads everything. A layered jar splits dependencies (rarely change) from application code (change often).

- Layers in order of change frequency: `dependencies`, `spring-boot-loader`, `snapshot-dependencies`, `application`.
- `java -Djarmode=tools -jar app.jar extract --layers` (Boot 3.3+) or `-Djarmode=layertools ... extract` (earlier) unpacks them.
- Use a multi-stage build, a small JRE base image, and a non-root user.
- Set JVM memory relative to the container: `-XX:MaxRAMPercentage=75`. Java 10+ is container aware.
- Alternative: `mvn spring-boot:build-image` (Cloud Native Buildpacks) builds an image without a Dockerfile.

```dockerfile
# ---- build stage ----
FROM eclipse-temurin:21-jdk AS build
WORKDIR /workspace
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
COPY src src
RUN ./mvnw -q -DskipTests package

# ---- extract layers ----
FROM eclipse-temurin:21-jre AS layers
WORKDIR /layers
COPY --from=build /workspace/target/order-service.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract
# Boot 3.3+ prefers: java -Djarmode=tools -jar app.jar extract --layers --launcher

# ---- runtime stage ----
FROM eclipse-temurin:21-jre
WORKDIR /app
RUN useradd --system --uid 1001 appuser
COPY --from=layers /layers/dependencies/ ./
COPY --from=layers /layers/spring-boot-loader/ ./
COPY --from=layers /layers/snapshot-dependencies/ ./
COPY --from=layers /layers/application/ ./
USER appuser
EXPOSE 8080
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "org.springframework.boot.loader.launch.JarLauncher"]
```

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <layers>
            <enabled>true</enabled>
            <!-- enabled by default since Boot 2.4 -->
        </layers>
    </configuration>
</plugin>
```

### 9.5 Externalized config and secrets

The same image must run in every environment. Only configuration changes, and it comes from outside the image.

- Follow the 12-factor rule: config in the environment, not in the code or the image.
- Order of use: environment variables, mounted files, Config Server, Vault. See precedence in [2.4](#24-configuration-files-and-precedence).
- Never commit passwords, API keys or JWT secrets to Git, and never bake them into the image.
- Kubernetes: ConfigMap for normal config, Secret for sensitive values (Secrets are only base64, so enable encryption at rest and RBAC). Better: AWS Secrets Manager / Parameter Store or HashiCorp Vault, injected at runtime.
- `spring.config.import=configtree:/run/secrets/` reads mounted secret files (one file per property) in Boot 2.4+.
- Do not expose `/actuator/env` and `/configprops` publicly. Since Boot 3.0 these endpoints hide all values by default (`management.endpoint.env.show-values` and `management.endpoint.configprops.show-values` default to `NEVER`), but treat them as sensitive.

```yaml
# Kubernetes Deployment (fragment)
env:
  - name: SPRING_DATASOURCE_URL
    valueFrom:
      configMapKeyRef:
        name: order-config
        key: db-url
  - name: SPRING_DATASOURCE_PASSWORD
    valueFrom:
      secretKeyRef:
        name: order-secrets
        key: db-password
  - name: APP_JWT_SECRET
    valueFrom:
      secretKeyRef:
        name: order-secrets
        key: jwt-secret
```

```properties
spring.datasource.url=${DB_URL}
spring.datasource.password=${DB_PASSWORD}
# resolved from environment variables at startup; startup fails if missing and no default is given
```

### 9.6 Common production problems

| Symptom | Usual cause | What to check or do |
|---|---|---|
| Requests hang, then time out | No timeouts on outgoing HTTP calls; Tomcat threads all blocked | Set connect/read timeouts, circuit breaker, thread dump (`/actuator/threaddump`) |
| `Connection is not available, request timed out` (Hikari) | Pool exhausted: long transactions, slow queries, leak, pool too small for load | Hikari metrics, `leak-detection-threshold`, shorten transactions, no remote calls inside `@Transactional` |
| `OutOfMemoryError: Java heap space` | Memory leak, huge result sets, big PDFs loaded fully | Heap dump, `-XX:+HeapDumpOnOutOfMemoryError`, stream or page data. See [JAVA.md memory leaks](../java/JAVA.md#27-memory-leaks) |
| Container killed with exit 137 (OOMKilled) | Container memory limit lower than JVM total memory (heap plus metaspace, threads, direct buffers) | Set `MaxRAMPercentage` below 100, raise the limit, check off-heap use |
| `LazyInitializationException` | Lazy field read outside a transaction/session | Fetch join, `@EntityGraph`, or map to DTO inside the service |
| Slow list endpoints, many SQL statements | N+1 queries | See [4.3](#43-lazy-vs-eager-loading-and-the-n1-problem) |
| `@Transactional` / `@Cacheable` / `@Async` has no effect | Self-invocation, private or final method, bean not created by Spring | See [5.1](#51-proxies-jdk-vs-cglib) |
| Kafka consumer lag grows, rebalances | Slow processing exceeds `max.poll.interval.ms`; poison message blocking a partition | Smaller batches, DLT, more partitions and consumers, idempotent processing |
| Duplicate processing of Kafka events | At-least-once delivery | Idempotent consumer, unique key on processed event id |
| Cache returns stale data | No TTL or eviction on update | `@CacheEvict`, TTL, versioned keys |
| Scheduled job runs several times | Multiple instances each run the job | ShedLock or database lock, or a single scheduler instance |
| Slow startup, probe kills the pod | Liveness probe starts before the app is ready | `startupProbe`, larger `initialDelaySeconds` |
| 401/403 only from browser, works in Postman | CORS preflight blocked by security | Enable `cors()` in `SecurityFilterChain`, see [6.6](#66-cors-and-csrf) |
| Cascading failure across services | One slow dependency uses all threads | Timeouts, circuit breaker, bulkhead, fallback, see [7.4](#74-resilience4j) |
| Time zone surprises | Server, JVM and DB use different zones | Store UTC, use `Instant`/`OffsetDateTime`, set `-Duser.timezone=UTC` |

Quick diagnosis tools:

```bash
curl http://localhost:9091/actuator/health
curl http://localhost:9091/actuator/threaddump
curl http://localhost:9091/actuator/metrics/hikaricp.connections.active
curl -X POST http://localhost:9091/actuator/loggers/com.example.order \
  -H "Content-Type: application/json" \
  -d '{"configuredLevel": "DEBUG"}'
# turns on DEBUG logging for one package without a restart
```

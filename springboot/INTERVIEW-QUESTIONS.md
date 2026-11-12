# Spring Boot Interview Questions (Senior Backend)

Target: backend developer with 3+ years of experience (Java, Spring Boot, REST, microservices, Spring Security with JWT, JPA/MySQL, Kafka, Docker/Kubernetes, Prometheus/Grafana, Jenkins, AWS).

Each question has a short answer, a simple explanation, code or a diagram, and follow-up questions with brief answers.

Examples use the same setup as the Kafka notes:

- Services: Order-Service (producer) and Inventory-Service (consumer), plus Payment-Service in the design questions.
- Domain: Order, OrderItem, Customer. Topic: `order-events`.

Target version is Spring Boot 3.x (Java 17+, `jakarta.*` packages). Version-specific facts are marked "Version note". Nothing here was run by the author of this file. Property names and behaviour can change between 3.x releases, so check the reference docs for your exact version. This file gives interview-style answers. For long explanations see the Spring Boot study notes (SPRINGBOOT.md).

---

## Index

- [Spring Core and Boot Internals](#spring-core-and-boot-internals)
  - Basic
    - [Q1. What are IoC and dependency injection in Spring? [Basic]](#q1-what-are-ioc-and-dependency-injection-in-spring-basic)
    - [Q2. Constructor injection vs field injection: which do you prefer and why? [Basic]](#q2-constructor-injection-vs-field-injection-which-do-you-prefer-and-why-basic)
    - [Q3. What is the difference between @Component and @Bean? [Basic]](#q3-what-is-the-difference-between-component-and-bean-basic)
    - [Q4. What does @SpringBootApplication contain, and what is a starter? [Basic]](#q4-what-does-springbootapplication-contain-and-what-is-a-starter-basic)
    - [Q5. @ConfigurationProperties vs @Value: when to use which? [Basic]](#q5-configurationproperties-vs-value-when-to-use-which-basic)
  - Intermediate
    - [Q6. How do profiles and configuration precedence work? [Intermediate]](#q6-how-do-profiles-and-configuration-precedence-work-intermediate)
    - [Q7. What are bean scopes, and what goes wrong with a prototype inside a singleton? [Intermediate]](#q7-what-are-bean-scopes-and-what-goes-wrong-with-a-prototype-inside-a-singleton-intermediate)
    - [Q8. Explain the bean lifecycle. [Intermediate]](#q8-explain-the-bean-lifecycle-intermediate)
    - [Q9. How do @Primary and @Qualifier work when there are several beans of one type? [Intermediate]](#q9-how-do-primary-and-qualifier-work-when-there-are-several-beans-of-one-type-intermediate)
    - [Q10. What are circular dependencies and how do you fix them? [Intermediate]](#q10-what-are-circular-dependencies-and-how-do-you-fix-them-intermediate)
    - [Q11. What happens when you run SpringApplication.run, and how does the embedded server work? [Intermediate]](#q11-what-happens-when-you-run-springapplicationrun-and-how-does-the-embedded-server-work-intermediate)
  - Advanced
    - [Q12. What does proxyBeanMethods do on @Configuration? [Advanced]](#q12-what-does-proxybeanmethods-do-on-configuration-advanced)
    - [Q13. How does Spring Boot auto-configuration work? [Advanced]](#q13-how-does-spring-boot-auto-configuration-work-advanced)
    - [Q14. How do you write a custom starter or auto-configuration? [Advanced]](#q14-how-do-you-write-a-custom-starter-or-auto-configuration-advanced)

- [REST and Web Layer](#rest-and-web-layer)
  - Basic
    - [Q15. How does a request flow through Spring MVC (DispatcherServlet)? [Basic]](#q15-how-does-a-request-flow-through-spring-mvc-dispatcherservlet-basic)
    - [Q16. @RestController vs @Controller, and @RequestBody, @PathVariable, @RequestParam [Basic]](#q16-restcontroller-vs-controller-and-requestbody-pathvariable-requestparam-basic)
    - [Q17. How does request validation work? [Basic]](#q17-how-does-request-validation-work-basic)
    - [Q18. Why use DTOs instead of returning JPA entities? [Basic]](#q18-why-use-dtos-instead-of-returning-jpa-entities-basic)
  - Intermediate
    - [Q19. How do you handle exceptions globally, and what is ProblemDetail? [Intermediate]](#q19-how-do-you-handle-exceptions-globally-and-what-is-problemdetail-intermediate)
    - [Q20. Filter vs HandlerInterceptor vs AOP: what is the difference? [Intermediate]](#q20-filter-vs-handlerinterceptor-vs-aop-what-is-the-difference-intermediate)
    - [Q21. How do you implement pagination? Offset vs keyset [Intermediate]](#q21-how-do-you-implement-pagination-offset-vs-keyset-intermediate)
    - [Q22. How do you call other services: RestTemplate, RestClient, WebClient, OpenFeign? Timeouts? [Intermediate]](#q22-how-do-you-call-other-services-resttemplate-restclient-webclient-openfeign-timeouts-intermediate)
  - Advanced
    - [Q23. How do you handle duplicate requests and make an API idempotent? [Advanced]](#q23-how-do-you-handle-duplicate-requests-and-make-an-api-idempotent-advanced)

- [Data and Transactions](#data-and-transactions)
  - Basic
    - [Q24. How does Spring Data JPA work? [Basic]](#q24-how-does-spring-data-jpa-work-basic)
    - [Q25. Lazy vs eager loading, and what is LazyInitializationException? [Basic]](#q25-lazy-vs-eager-loading-and-what-is-lazyinitializationexception-basic)
  - Intermediate
    - [Q26. What is the N+1 problem and how do you fix it? [Intermediate]](#q26-what-is-the-n1-problem-and-how-do-you-fix-it-intermediate)
    - [Q27. How does @Transactional work, and what are the rollback rules? [Intermediate]](#q27-how-does-transactional-work-and-what-are-the-rollback-rules-intermediate)
    - [Q28. Explain transaction propagation and isolation levels. [Intermediate]](#q28-explain-transaction-propagation-and-isolation-levels-intermediate)
    - [Q29. Why does @Transactional not work with self-invocation or on private methods? [Intermediate]](#q29-why-does-transactional-not-work-with-self-invocation-or-on-private-methods-intermediate)
    - [Q30. What is open-in-view and should you turn it off? [Intermediate]](#q30-what-is-open-in-view-and-should-you-turn-it-off-intermediate)
    - [Q31. How do you manage schema changes with Flyway? [Intermediate]](#q31-how-do-you-manage-schema-changes-with-flyway-intermediate)
  - Advanced
    - [Q32. How do optimistic and pessimistic locking work? [Advanced]](#q32-how-do-optimistic-and-pessimistic-locking-work-advanced)
    - [Q33. How do you do fast batch inserts with JPA? [Advanced]](#q33-how-do-you-do-fast-batch-inserts-with-jpa-advanced)
    - [Q34. How do you tune HikariCP and diagnose "connection is not available"? [Advanced]](#q34-how-do-you-tune-hikaricp-and-diagnose-connection-is-not-available-advanced)

- [AOP, Proxies, Caching and Async](#aop-proxies-caching-and-async)
  - Basic
    - [Q35. What is AOP and where does Spring use it? [Basic]](#q35-what-is-aop-and-where-does-spring-use-it-basic)
  - Intermediate
    - [Q36. JDK dynamic proxy vs CGLIB: what is the difference? [Intermediate]](#q36-jdk-dynamic-proxy-vs-cglib-what-is-the-difference-intermediate)
    - [Q37. What are good uses of @Aspect, and how do you write one? [Intermediate]](#q37-what-are-good-uses-of-aspect-and-how-do-you-write-one-intermediate)
    - [Q38. How does caching work with @Cacheable and Redis? [Intermediate]](#q38-how-does-caching-work-with-cacheable-and-redis-intermediate)
    - [Q39. How does @Async work, and how do you configure its thread pool? [Intermediate]](#q39-how-does-async-work-and-how-do-you-configure-its-thread-pool-intermediate)
  - Advanced
    - [Q40. How do you run @Scheduled jobs when there are several instances? [Advanced]](#q40-how-do-you-run-scheduled-jobs-when-there-are-several-instances-advanced)
    - [Q41. What cache problems should you know: stampede, penetration, avalanche, stale data? [Advanced]](#q41-what-cache-problems-should-you-know-stampede-penetration-avalanche-stale-data-advanced)

- [Security](#security)
  - Basic
    - [Q42. How does Spring Security work? Filter chain, authentication vs authorization [Basic]](#q42-how-does-spring-security-work-filter-chain-authentication-vs-authorization-basic)
    - [Q43. How should passwords be stored? [Basic]](#q43-how-should-passwords-be-stored-basic)
  - Intermediate
    - [Q44. How do you configure Spring Security 6 with a SecurityFilterChain? [Intermediate]](#q44-how-do-you-configure-spring-security-6-with-a-securityfilterchain-intermediate)
    - [Q45. Walk through a JWT authentication flow in Spring Boot. [Intermediate]](#q45-walk-through-a-jwt-authentication-flow-in-spring-boot-intermediate)
    - [Q46. What is the difference between CORS and CSRF? [Intermediate]](#q46-what-is-the-difference-between-cors-and-csrf-intermediate)
    - [Q47. How does method security (@PreAuthorize) work? [Intermediate]](#q47-how-does-method-security-preauthorize-work-intermediate)
  - Advanced
    - [Q48. What are the JWT pitfalls: storage, expiry, refresh tokens, revocation? [Advanced]](#q48-what-are-the-jwt-pitfalls-storage-expiry-refresh-tokens-revocation-advanced)
    - [Q49. What is an OAuth2 / OIDC resource server in Spring? [Advanced]](#q49-what-is-an-oauth2--oidc-resource-server-in-spring-advanced)
    - [Q50. Scenario: how do you secure a set of microservices? [Advanced]](#q50-scenario-how-do-you-secure-a-set-of-microservices-advanced)

- [Microservices with Spring](#microservices-with-spring)
  - Basic
    - [Q51. What are service discovery, API gateway and config server? [Basic]](#q51-what-are-service-discovery-api-gateway-and-config-server-basic)
  - Intermediate
    - [Q52. How does a circuit breaker work with Resilience4j? [Intermediate]](#q52-how-does-a-circuit-breaker-work-with-resilience4j-intermediate)
    - [Q53. How do retry, bulkhead, rate limiter and time limiter work, and in what order do they apply? [Intermediate]](#q53-how-do-retry-bulkhead-rate-limiter-and-time-limiter-work-and-in-what-order-do-they-apply-intermediate)
    - [Q54. How does distributed tracing work in Spring Boot 3? [Intermediate]](#q54-how-does-distributed-tracing-work-in-spring-boot-3-intermediate)
    - [Q55. How do you configure a Spring Kafka consumer, handle errors and use a dead-letter topic? [Intermediate]](#q55-how-do-you-configure-a-spring-kafka-consumer-handle-errors-and-use-a-dead-letter-topic-intermediate)
  - Advanced
    - [Q56. How do you make a Kafka consumer idempotent, and what about Kafka transactions? [Advanced]](#q56-how-do-you-make-a-kafka-consumer-idempotent-and-what-about-kafka-transactions-advanced)
    - [Q57. How do you implement the outbox pattern and a saga in Spring? [Advanced]](#q57-how-do-you-implement-the-outbox-pattern-and-a-saga-in-spring-advanced)
    - [Q58. How do you keep an API backward compatible and version it? [Advanced]](#q58-how-do-you-keep-an-api-backward-compatible-and-version-it-advanced)
    - [Q59. Scenario: design an Order Service that must not lose or duplicate orders [Advanced]](#q59-scenario-design-an-order-service-that-must-not-lose-or-duplicate-orders-advanced)
    - [Q60. Scenario: how do you migrate one piece of a monolith to a microservice? [Advanced]](#q60-scenario-how-do-you-migrate-one-piece-of-a-monolith-to-a-microservice-advanced)

- [Testing and Production](#testing-and-production)
  - Basic
    - [Q61. Unit test vs slice test vs @SpringBootTest: what do you use when? [Basic]](#q61-unit-test-vs-slice-test-vs-springboottest-what-do-you-use-when-basic)
    - [Q62. What is Spring Boot Actuator, and how do health probes work in Kubernetes? [Basic]](#q62-what-is-spring-boot-actuator-and-how-do-health-probes-work-in-kubernetes-basic)
  - Intermediate
    - [Q63. How do you test controllers and repositories with MockMvc, @MockitoBean and Testcontainers? [Intermediate]](#q63-how-do-you-test-controllers-and-repositories-with-mockmvc-mockitobean-and-testcontainers-intermediate)
    - [Q64. How do you set up metrics with Micrometer, Prometheus and Grafana? [Intermediate]](#q64-how-do-you-set-up-metrics-with-micrometer-prometheus-and-grafana-intermediate)
    - [Q65. How do you do graceful shutdown and package Spring Boot in Docker, including JVM memory in containers? [Intermediate]](#q65-how-do-you-do-graceful-shutdown-and-package-spring-boot-in-docker-including-jvm-memory-in-containers-intermediate)
  - Advanced
    - [Q66. How can you reduce startup time: lazy init, CDS and GraalVM native image? [Advanced]](#q66-how-can-you-reduce-startup-time-lazy-init-cds-and-graalvm-native-image-advanced)
    - [Q67. Scenario: one endpoint is slow in production. How do you debug it? [Advanced]](#q67-scenario-one-endpoint-is-slow-in-production-how-do-you-debug-it-advanced)
    - [Q68. How do you debug a memory leak, connection pool exhaustion and thread starvation? [Advanced]](#q68-how-do-you-debug-a-memory-leak-connection-pool-exhaustion-and-thread-starvation-advanced)
    - [Q69. Scenario: 5xx errors spike after a release, or the app starts slowly. What do you do? [Advanced]](#q69-scenario-5xx-errors-spike-after-a-release-or-the-app-starts-slowly-what-do-you-do-advanced)

---

# Spring Core and Boot Internals

---

### Q1. What are IoC and dependency injection in Spring? [Basic]

**Level: Basic**

**Short answer:** IoC (Inversion of Control) means the Spring container creates objects and wires them together, not your code. DI (dependency injection) is how it does this: it passes the objects a class needs through its constructor, setter or field.

**Explanation:**

- The container is called the **ApplicationContext**. Objects it manages are called **beans**.
- Without DI, a class creates its own dependencies with `new`. That makes it hard to replace them in tests.
- With DI, the class only declares what it needs. Spring finds a matching bean and gives it.
- Spring decides which bean to inject mainly by **type**, then by name or qualifier if there is more than one match.

```java
@Service
public class OrderService {

    private final InventoryClient inventoryClient;

    public OrderService(InventoryClient inventoryClient) {
        this.inventoryClient = inventoryClient;
    }
}
// Spring creates InventoryClient first, then passes it to OrderService.
```

**Follow-up questions:**

- **What is the difference between BeanFactory and ApplicationContext?**
  BeanFactory is the basic container. ApplicationContext adds events, internationalisation, profiles and automatic post-processors. You almost always use ApplicationContext.
- **How does Spring find beans to create?**
  Component scanning finds classes with stereotype annotations (`@Component`, `@Service`, `@Repository`, `@Controller`) under the main class package. `@Bean` methods and auto-configuration add more.
- **Does a class need `@Autowired` to receive injection?**
  The class itself must be a bean. With one constructor, `@Autowired` is optional (since Spring 4.3).

---

### Q2. Constructor injection vs field injection: which do you prefer and why? [Basic]

**Level: Basic**

**Short answer:** Prefer constructor injection. It makes dependencies explicit and required, allows `final` fields, and lets you test the class without Spring.

**Explanation:**

- **Constructor injection:** dependencies come in the constructor. The object is never half-built.
- **Field injection:** `@Autowired` on a private field. Spring sets it using reflection. You cannot make it `final`, and in a plain unit test you cannot set it without reflection or Mockito tricks.
- **Setter injection:** good for optional dependencies that can change.
- Constructor injection also shows circular dependencies early, at startup (see Q10).
- A constructor with too many parameters is a signal that the class does too much.

```java
// Field injection (avoid)
@Service
public class OrderService {

    @Autowired
    private InventoryClient inventoryClient;
}

// Constructor injection (prefer)
@Service
public class OrderService {

    private final InventoryClient inventoryClient;

    public OrderService(InventoryClient inventoryClient) {
        this.inventoryClient = inventoryClient;
    }
}

// Unit test without Spring:
// OrderService service = new OrderService(mock(InventoryClient.class));
```

**Follow-up questions:**

- **Does Lombok help here?**
  `@RequiredArgsConstructor` generates a constructor for all `final` fields. The result is the same as writing it by hand.
- **When is field injection acceptable?**
  In test classes (`@Autowired` in a `@SpringBootTest`). Avoid it in production code.
- **What if there are two implementations of one type?**
  Use `@Qualifier` or `@Primary` (see Q9).

---

### Q3. What is the difference between @Component and @Bean? [Basic]

**Level: Basic**

**Short answer:** `@Component` marks YOUR class so component scanning registers it. `@Bean` is placed on a method in a `@Configuration` class and registers the object that method returns. Use `@Bean` for classes you do not own.

**Explanation:**

- `@Component` works at class level. `@Service`, `@Repository` and `@Controller` are specialisations of it. `@Repository` also translates persistence exceptions.
- `@Bean` works at method level. You write the creation code, so you control constructor arguments and setup.
- Use `@Bean` for third-party classes (`ObjectMapper`, `RestClient`, `ModelMapper`) or when creation needs logic.

```java
@Configuration
public class AppConfig {

    @Bean
    public RestClient inventoryRestClient(RestClient.Builder builder) {
        return builder
            .baseUrl("http://inventory-service")
            .build();
    }
}
```

**Follow-up questions:**

- **What is the bean name by default?**
  For `@Component`, the class name with a lowercase first letter (`orderService`). For `@Bean`, the method name.
- **Can `@Bean` be used in a `@Component` class?**
  Yes, but this is "lite mode": no proxy, so calls between `@Bean` methods create new objects (see Q12).

---

### Q4. What does @SpringBootApplication contain, and what is a starter? [Basic]

**Level: Basic**

**Short answer:** `@SpringBootApplication` combines `@SpringBootConfiguration`, `@EnableAutoConfiguration` and `@ComponentScan`. A starter is a dependency that pulls in a set of libraries for one purpose, such as web or JPA.

**Explanation:**

- `@SpringBootConfiguration`: marks the class as a configuration class (it is a `@Configuration`).
- `@ComponentScan`: scans the package of this class and its sub-packages. Beans outside that package are NOT found.
- `@EnableAutoConfiguration`: turns on auto-configuration (Q13).
- A **starter** (for example `spring-boot-starter-web`) is only a dependency list. It brings Spring MVC, Jackson and embedded Tomcat with compatible versions. The Boot parent or BOM manages versions.

```java
@SpringBootApplication
public class OrderServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderServiceApplication.class, args);
    }
}
```

**Follow-up questions:**

- **Why are my beans not found?**
  They are probably outside the main class package. Move the main class up or set `scanBasePackages`.
- **How do you exclude an auto-configuration?**
  `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)` or the property `spring.autoconfigure.exclude`.
- **What does a starter contain?**
  Usually no code. Just dependencies. The auto-configuration lives in the `spring-boot-autoconfigure` module.

---

### Q5. @ConfigurationProperties vs @Value: when to use which? [Basic]

**Level: Basic**

**Short answer:** Use `@Value` for one simple value. Use `@ConfigurationProperties` for a group of related settings, because it is type-safe, supports validation and nested objects, and is easier to test.

**Explanation:**

- `@Value("${key}")` injects one property. It supports SpEL (Spring Expression Language, `#{...}`) and defaults, but has no structure.
- `@ConfigurationProperties(prefix = "...")` binds a whole section into a class. It uses **relaxed binding**: `max-retries`, `maxRetries` and `MAX_RETRIES` all map to the same field.
- Constructor binding with a `record` is supported in Boot 3.

```yaml
inventory:
  base-url: http://inventory-service:8080
  connect-timeout: 2s
  read-timeout: 5s
```

```java
@Validated
@ConfigurationProperties(prefix = "inventory")
public record InventoryProperties(
    @NotBlank String baseUrl,
    Duration connectTimeout,
    Duration readTimeout
) {
}

// Register it on the main class or any @Configuration:
// @EnableConfigurationProperties(InventoryProperties.class)
// or @ConfigurationPropertiesScan
```

**Follow-up questions:**

- **How do you fail fast on a missing required property?**
  Add `@Validated` with constraints like `@NotBlank`. The app fails at startup with a clear message.
- **Can `@ConfigurationProperties` reload at runtime?**
  Not by default. Spring Cloud `@RefreshScope` with Config Server or Actuator refresh can do it (Q51).
- **Where should secrets come from?**
  Environment variables, Kubernetes Secrets or AWS Secrets Manager. Never commit them to `application.yml`.

---

### Q6. How do profiles and configuration precedence work? [Intermediate]

**Level: Intermediate**

**Short answer:** Profiles switch groups of beans and properties per environment (`dev`, `prod`). When the same property is set in several places, the more specific source wins: command-line arguments beat environment variables, which beat `application-{profile}.yml`, which beats `application.yml`.

**Explanation:**

Simplified order, highest priority first:

```text
1. Command-line arguments            --server.port=9090
2. SPRING_APPLICATION_JSON (inline JSON in an env var or system property)
3. JVM system properties (-Dkey=value), then OS environment variables  SERVER_PORT=9090
   (system properties beat environment variables)
4. application-{profile}.yml/.properties  (outside the jar, then inside)
5. application.yml/.properties            (outside the jar, then inside)
6. @PropertySource and defaults set in code
```

- Activate a profile with `spring.profiles.active=prod` or env var `SPRING_PROFILES_ACTIVE=prod`.
- `@Profile("dev")` on a bean or configuration class creates it only in that profile.
- Environment variables map to properties: `SPRING_DATASOURCE_URL` becomes `spring.datasource.url`. This is how you configure a container (Docker, Kubernetes) without rebuilding.
- Check the exact order in the Spring Boot "Externalized Configuration" page for your version. Boot 3.x also supports `spring.config.import` (for example `configtree:` for mounted secrets, or `optional:configserver:`).

```java
@Configuration
@Profile("dev")
public class DevDataConfig {

    @Bean
    CommandLineRunner seedOrders(OrderRepository repository) {
        return args -> {
            repository.save(new Order());
        };
    }
}
```

**Follow-up questions:**

- **Is one file per profile good practice?**
  It is common. Keep secrets out of those files. Use environment variables for anything environment specific, like URLs and credentials.
- **Can you activate several profiles?**
  Yes: `spring.profiles.active=prod,kafka`. Profile groups (`spring.profiles.group`) bundle them.
- **Properties vs YAML: which wins in one location?**
  `.properties` wins over `.yml` in the same location.

---

### Q7. What are bean scopes, and what goes wrong with a prototype inside a singleton? [Intermediate]

**Level: Intermediate**

**Short answer:** The default scope is `singleton`: one instance per ApplicationContext. `prototype` creates a new instance each time the bean is requested. Web scopes are `request` and `session`. If a singleton holds a prototype, it gets only ONE prototype instance, injected once at startup.

**Explanation:**

| Scope | Instances | Notes |
|---|---|---|
| singleton (default) | One per context | Must be thread-safe. No per-request state in fields. |
| prototype | New one each time it is requested | Spring does not call destroy callbacks on it |
| request / session | One per HTTP request / session | Web apps only |

- Singleton does NOT mean the Gang-of-Four singleton. It is one per container.
- The common bug: storing request data in a field of a singleton service. Two threads overwrite each other.
- To get a fresh prototype from a singleton, inject an `ObjectProvider<T>` and call `getObject()`, or use a scoped proxy.

```java
@Component
@Scope("prototype")
public class OrderContext {
}

@Service
public class OrderService {

    private final ObjectProvider<OrderContext> contextProvider;

    public OrderService(ObjectProvider<OrderContext> contextProvider) {
        this.contextProvider = contextProvider;
    }

    public void process() {
        OrderContext context = contextProvider.getObject();
        // new OrderContext each call
    }
}
```

**Follow-up questions:**

- **Is a singleton bean thread-safe?**
  Only if it is stateless or its state is immutable or guarded. Spring does not add thread safety.
- **What is a scoped proxy?**
  `@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)` injects a proxy that looks up the real request-scoped bean on each call.
- **Are controllers singletons?**
  Yes. One controller instance serves all requests.

---

### Q8. Explain the bean lifecycle. [Intermediate]

**Level: Intermediate**

**Short answer:** Spring creates the bean, injects dependencies, runs initialisation callbacks (`@PostConstruct` and others), and the bean is ready. On shutdown it runs destruction callbacks (`@PreDestroy`). `BeanPostProcessor`s run around initialisation and create proxies.

**Explanation:**

```text
Instantiate (constructor)
    |
Populate properties (inject dependencies)
    |
Aware callbacks (BeanNameAware, ApplicationContextAware ...)
    |
BeanPostProcessor.postProcessBeforeInitialization
    |
@PostConstruct
    |
InitializingBean.afterPropertiesSet()
    |
Custom init-method (@Bean(initMethod = "..."))
    |
BeanPostProcessor.postProcessAfterInitialization   <-- AOP proxies are created here
    |
Bean is READY and in use
    |
@PreDestroy -> DisposableBean.destroy() -> custom destroy-method   (on context close)
```

- A **BeanPostProcessor** is a hook that sees every bean after creation. `@Transactional`, `@Async` and `@Cacheable` work because a post-processor wraps your bean in a proxy (Q35).
- With field injection, dependencies are not set yet inside the constructor body. Use `@PostConstruct` for startup logic. With constructor injection they are available.
- Keep `@PostConstruct` fast. Slow work there makes startup slow. Calls to remote services there can fail the whole startup.
- `@PostConstruct` and `@PreDestroy` live in `jakarta.annotation` in Boot 3 (they were `javax.annotation` in Boot 2).

```java
@Component
public class InventoryCache {

    private Map<String, Integer> stock;

    @PostConstruct
    void load() {
        stock = new ConcurrentHashMap<>();
        // load initial data
    }

    @PreDestroy
    void close() {
        stock.clear();
    }
}
```

**Follow-up questions:**

- **Why is `@Transactional` not active inside `@PostConstruct`?**
  At that point the proxy does not exist yet (it is created after initialisation). Use `ApplicationReadyEvent` or `SmartInitializingSingleton` instead.
- **What is the difference between `BeanFactoryPostProcessor` and `BeanPostProcessor`?**
  The first changes bean DEFINITIONS before any bean is created (for example resolving `${...}` placeholders). The second changes bean INSTANCES after creation.
- **Does destroy run for prototype beans?**
  No. Spring does not manage prototype beans after creating them.
- **How do you run code when the app is fully started?**
  `ApplicationRunner`, `CommandLineRunner` or `@EventListener(ApplicationReadyEvent.class)`.

---

### Q9. How do @Primary and @Qualifier work when there are several beans of one type? [Intermediate]

**Level: Intermediate**

**Short answer:** If Spring finds more than one bean of the requested type it throws `NoUniqueBeanDefinitionException`. `@Primary` marks the default choice. `@Qualifier("name")` at the injection point selects a specific one and wins over `@Primary`.

**Explanation:**

- Example: two `PaymentClient` implementations, `StripeClient` and `PaypalClient`.
- Spring also falls back to matching the parameter NAME with the bean name (works if compiled with `-parameters`, which Boot's build plugins do). Do not rely on it.
- You can inject all of them: `List<PaymentClient>` or `Map<String, PaymentClient>` (bean name to bean). This is handy for strategy patterns.

```java
@Component
@Primary
public class StripeClient implements PaymentClient {
}

@Component("paypalClient")
public class PaypalClient implements PaymentClient {
}

@Service
public class PaymentService {

    private final PaymentClient defaultClient;
    private final PaymentClient paypal;

    public PaymentService(
        PaymentClient defaultClient,
        @Qualifier("paypalClient") PaymentClient paypal
    ) {
        this.defaultClient = defaultClient;
        this.paypal = paypal;
    }
}
// defaultClient is StripeClient (the @Primary). paypal is PaypalClient.
```

**Follow-up questions:**

- **What does `@ConditionalOnMissingBean` have to do with this?**
  Auto-configuration uses it to back off when you define your own bean of that type (Q13).
- **How do you make a dependency optional?**
  `ObjectProvider<T>`, `Optional<T>`, or `@Autowired(required = false)`.

---

### Q10. What are circular dependencies and how do you fix them? [Intermediate]

**Level: Intermediate**

**Short answer:** A circular dependency is when bean A needs B and B needs A. With constructor injection Spring cannot create either and fails at startup. Since Boot 2.6 circular references are forbidden by default. The real fix is to change the design.

**Explanation:**

- Field and setter injection used to hide the cycle because Spring could create a half-built bean first (the "early reference"). Boot 2.6+ disables this: `spring.main.allow-circular-references=false` is the default.
- Typical error: `BeanCurrentlyInCreationException` ("The dependencies of some of the beans in the application context form a cycle").
- Fixes, best first:
  1. Redesign: extract the shared logic into a third bean C that both use.
  2. Use events: A publishes an event, B listens.
  3. Inject one side with `@Lazy` (a lazy proxy is injected and the real bean is resolved on first use).
  4. Setter or field injection for one side only works after setting `spring.main.allow-circular-references=true`. That is a last resort.

```java
@Service
public class OrderService {

    private final CustomerService customerService;

    public OrderService(@Lazy CustomerService customerService) {
        this.customerService = customerService;
    }
}
// CustomerService also needs OrderService. @Lazy breaks the startup cycle.
```

**Follow-up questions:**

- **Why is a cycle a design smell?**
  Two classes that depend on each other are really one unit, or they are missing a shared abstraction.
- **Does a cycle work for prototype beans?**
  No. Spring cannot resolve it for prototypes at all.

---

### Q11. What happens when you run SpringApplication.run, and how does the embedded server work? [Intermediate]

**Level: Intermediate**

**Short answer:** `SpringApplication.run` prepares the environment (properties, profiles), creates the ApplicationContext, loads beans and auto-configuration, starts the embedded web server (Tomcat by default) and then runs runners. The app is a plain Java program with the server inside it, so you deploy a single executable JAR.

**Explanation:**

```text
main()
  -> SpringApplication.run
       1. Create the Environment (read properties, profiles)
       2. Print banner, create ApplicationContext
       3. Refresh the context:
            - load bean definitions (scan + @Bean + auto-configuration)
            - run BeanFactoryPostProcessors
            - create singleton beans (lifecycle in Q8)
            - start the embedded web server (Tomcat) on server.port
       4. Run CommandLineRunner / ApplicationRunner
       5. Publish ApplicationReadyEvent
```

- The web server comes from `spring-boot-starter-web` (Tomcat). To use Jetty, exclude Tomcat and add the Jetty starter. (Support for other servers depends on the Servlet API version of your Boot release, so check.)
- Tomcat tuning: `server.tomcat.threads.max` (default 200), `server.tomcat.accept-count`, `server.tomcat.max-connections`.
- The executable JAR is a "fat JAR" built by the Boot Maven/Gradle plugin, with nested dependency JARs and a launcher class.
- **Boot 3.2+ with Java 21:** `spring.threads.virtual.enabled=true` runs request handling on virtual threads. Measure before you adopt it, and watch for blocking code that pins carrier threads.

**Follow-up questions:**

- **How do you run with a different server port?**
  `server.port=8081` in config, or `--server.port=8081`. Use `server.port=0` for a random port in tests.
- **WAR vs JAR?**
  JAR with embedded server is the normal choice for containers. WAR is only for deploying to an external servlet container.
- **What changes for Boot 3?**
  Java 17 minimum, `javax.*` became `jakarta.*`, Spring Framework 6, Hibernate 6.

---

### Q12. What does proxyBeanMethods do on @Configuration? [Advanced]

**Level: Advanced**

**Short answer:** With `proxyBeanMethods = true` (the default) Spring subclasses the configuration class with CGLIB, so calling one `@Bean` method from another returns the same singleton. With `false` there is no proxy, so such a call creates a new object. `false` starts faster and uses less memory.

**Explanation:**

- "Full" mode (`true`): the proxy intercepts `@Bean` method calls and returns the cached bean.
- "Lite" mode (`false`, or a `@Component` class with `@Bean` methods): `@Bean` methods are plain methods. Calling one directly creates a new instance every time.
- Spring Boot's own auto-configuration classes use `proxyBeanMethods = false` for startup speed. They get dependencies as method parameters instead.
- Rule: with `false`, inject other beans through method parameters, never by calling the other `@Bean` method.

```java
@Configuration(proxyBeanMethods = false)
public class ClientConfig {

    @Bean
    public InventoryClient inventoryClient(RestClient restClient) {
        // restClient is injected by Spring (the same singleton)
        return new InventoryClient(restClient);
    }
}
```

```java
// Full mode (proxyBeanMethods = true): works, same RestClient instance
@Bean
public InventoryClient inventoryClient() {
    return new InventoryClient(restClient());
}

// Lite mode (false): this would create a SECOND RestClient. Do not do it.
```

**Follow-up questions:**

- **Why is the proxy needed in full mode?**
  Plain Java would run `restClient()` again and return a new object. The CGLIB subclass overrides it to return the existing bean.
- **Does this matter for GraalVM native image?**
  Yes. Fewer proxies means less to process at build time. This is one reason Boot uses `false` internally.

---

### Q13. How does Spring Boot auto-configuration work? [Advanced]

**Level: Advanced**

**Short answer:** Boot reads a list of auto-configuration classes from `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` in each JAR. Each class is full of `@Conditional...` annotations, so it creates beans only when the right classes are on the classpath, the right properties are set, and you have not defined your own bean.

**Explanation:**

- `@EnableAutoConfiguration` imports `AutoConfigurationImportSelector`. It loads all candidate classes from the `.imports` files.
- Each candidate is checked against its conditions. Only the ones that match are applied.
- **Version note (Boot 3):** auto-configuration is registered in `AutoConfiguration.imports`. Registering auto-configuration in `spring.factories` was deprecated in Boot 2.7 and is NOT supported in Boot 3. `spring.factories` is still used for other extension points.
- Important conditions:

| Annotation | Meaning |
|---|---|
| `@ConditionalOnClass` | A class is on the classpath (for example `DataSource`) |
| `@ConditionalOnMissingBean` | You did not define this bean. Lets you override defaults. |
| `@ConditionalOnProperty` | A property has a given value |
| `@ConditionalOnWebApplication` | It is a web app (servlet or reactive) |
| `@ConditionalOnBean` | Another bean exists (order-sensitive, use with care) |

- Example: add `spring-boot-starter-data-jpa` and a MySQL driver, and set `spring.datasource.url`. `DataSourceAutoConfiguration` creates a HikariCP `DataSource` because `DataSource` is on the classpath and you defined no `DataSource` bean.

```java
@AutoConfiguration
@ConditionalOnClass(KafkaTemplate.class)
@EnableConfigurationProperties(KafkaProperties.class)
public class KafkaAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean(KafkaTemplate.class)
    public KafkaTemplate<?, ?> kafkaTemplate(ProducerFactory<Object, Object> factory) {
        return new KafkaTemplate<>(factory);
    }
}
// Simplified from the real class. If you define your own KafkaTemplate, this one backs off.
```

See what was applied or skipped:

```bash
java -jar order-service.jar --debug
# prints the "Conditions Evaluation Report": positive matches and negative matches
```

**Follow-up questions:**

- **How do you override an auto-configured bean?**
  Define your own bean of the same type. `@ConditionalOnMissingBean` makes the default back off.
- **How do you find out why something was (not) configured?**
  Run with `--debug`, or use the Actuator `conditions` endpoint.
- **Does auto-configuration run before or after my beans?**
  After. User configuration is processed first, so `@ConditionalOnMissingBean` can see your beans.
- **How do you turn one off?**
  `exclude` on `@SpringBootApplication`, or `spring.autoconfigure.exclude`.

---

### Q14. How do you write a custom starter or auto-configuration? [Advanced]

**Level: Advanced**

**Short answer:** Create an auto-configuration class with conditions, list it in `AutoConfiguration.imports`, expose settings with `@ConfigurationProperties`, and package it (usually as `xyz-spring-boot-starter`). Teams use it to share audit logging, a standard `RestClient`, or common security setup across services.

**Explanation:**

- Library module: contains the auto-configuration and the properties class.
- Starter module (optional): an empty module that depends on the library and its dependencies.
- Always use `@ConditionalOnMissingBean` so applications can override your defaults.
- Keep it off by default if it is risky: `@ConditionalOnProperty(prefix = "audit", name = "enabled", havingValue = "true")`.

```java
@AutoConfiguration
@EnableConfigurationProperties(AuditProperties.class)
@ConditionalOnProperty(
    prefix = "audit",
    name = "enabled",
    havingValue = "true",
    matchIfMissing = true
)
public class AuditAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public AuditService auditService(AuditProperties properties) {
        return new AuditService(properties.topic());
    }
}
```

```text
# File: src/main/resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
com.example.audit.AuditAutoConfiguration
```

**Follow-up questions:**

- **How do you control the order of auto-configurations?**
  `@AutoConfiguration(after = ..., before = ...)`.
- **Why not just component-scan the shared library?**
  Scanning forces a package layout on every service and cannot back off conditionally. Auto-configuration is explicit and conditional.
- **How do you test an auto-configuration?**
  `ApplicationContextRunner` creates a small context, lets you set properties, and assert which beans exist.

# REST and Web Layer

---

### Q15. How does a request flow through Spring MVC (DispatcherServlet)? [Basic]

**Level: Basic**

**Short answer:** Every request goes to the `DispatcherServlet` (the front controller). It finds the right controller method, calls it, converts the result to JSON, and returns the response.

**Explanation:**

```text
Client
  |
Servlet filters (security, logging, CORS)
  |
DispatcherServlet
  |-- 1. HandlerMapping: which controller method handles GET /orders/5 ?
  |-- 2. Interceptors: preHandle()
  |-- 3. HandlerAdapter: resolve arguments (@PathVariable, @RequestBody via Jackson)
  |-- 4. Controller method runs
  |-- 5. Return value -> HttpMessageConverter -> JSON
  |-- 6. Interceptors: postHandle() / afterCompletion()
  |-- (on exception: HandlerExceptionResolver -> @ControllerAdvice)
  |
Response to client
```

- **HandlerMapping** matches URL, HTTP method and headers to a handler.
- **HttpMessageConverter** reads and writes the body. Jackson handles JSON.
- One thread from the Tomcat pool handles the request from start to finish (in the servlet stack).

**Follow-up questions:**

- **Where do servlet filters fit?**
  Before the DispatcherServlet. Interceptors run inside it (Q20).
- **What if no handler matches?**
  404. In Spring 6.1+ a missing static resource throws `NoResourceFoundException`, which is mapped to 404.
- **What is content negotiation?**
  Choosing the response format using the `Accept` header, for example JSON or XML.

---

### Q16. @RestController vs @Controller, and @RequestBody, @PathVariable, @RequestParam [Basic]

**Level: Basic**

**Short answer:** `@RestController` = `@Controller` + `@ResponseBody`: return values are written to the response body (JSON), not treated as view names. `@RequestBody` reads the JSON body, `@PathVariable` reads a value from the URL path, and `@RequestParam` reads a query parameter.

**Explanation:**

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
        // GET /api/orders/5 -> id = 5
        return orderService.get(id);
    }

    @GetMapping
    public List<OrderResponse> list(@RequestParam(defaultValue = "NEW") String status) {
        // GET /api/orders?status=PAID
        return orderService.findByStatus(status);
    }

    @PostMapping
    public ResponseEntity<OrderResponse> create(@Valid @RequestBody CreateOrderRequest request) {
        OrderResponse created = orderService.create(request);
        return ResponseEntity
            .created(URI.create("/api/orders/" + created.id()))
            .body(created);
        // 201 Created with a Location header
    }
}
```

- Use `ResponseEntity` when you need to control status code and headers.
- `@RequestHeader` reads a header. `@ModelAttribute` binds form data.
- Use correct status codes: 201 for create, 204 for no content, 404 not found, 409 conflict, 400 or 422 for validation.

**Follow-up questions:**

- **PUT vs PATCH vs POST?**
  PUT replaces a resource and is idempotent. PATCH changes part of it. POST creates or triggers actions and is not idempotent by default (see Q23).
- **What if the path variable name differs from the parameter?**
  Use `@PathVariable("orderId") Long id`.
- **What happens when the JSON is invalid?**
  `HttpMessageNotReadableException`, which maps to 400 Bad Request.

---

### Q17. How does request validation work? [Basic]

**Level: Basic**

**Short answer:** Add Bean Validation annotations (`@NotNull`, `@Size`, `@Min`) to the request DTO and put `@Valid` on the controller parameter. Spring rejects bad input with a 400 before your code runs.

**Explanation:**

- In Boot 3 the annotations are in `jakarta.validation.constraints`. You need `spring-boot-starter-validation`.
- `@Valid` on `@RequestBody` throws `MethodArgumentNotValidException`.
- To validate `@PathVariable` or `@RequestParam`, put constraints on the parameters (and `@Validated` on the class in older versions). **Version note:** Spring 6.1 (Boot 3.2) has built-in method validation that throws `HandlerMethodValidationException`. Before that you get `ConstraintViolationException`. Check which you get.
- Validate at the edge (controller). Business rules (stock is enough) belong in the service.

```java
public record CreateOrderRequest(

    @NotNull
    Long customerId,

    @NotEmpty
    List<@Valid OrderItemRequest> items
) {
}

public record OrderItemRequest(

    @NotBlank
    String sku,

    @Min(1)
    int quantity
) {
}
```

**Follow-up questions:**

- **How do you write a custom validator?**
  Create an annotation with `@Constraint(validatedBy = ...)` and a class implementing `ConstraintValidator`.
- **Validation groups?**
  Use different rule sets for create and update with `@Validated(OnCreate.class)`.
- **Is client-side validation enough?**
  No. Always validate on the server.

---

### Q18. Why use DTOs instead of returning JPA entities? [Basic]

**Level: Basic**

**Short answer:** A DTO (Data Transfer Object) is a class made for the API. Returning entities exposes internal fields, couples the API to the database schema, causes lazy-loading errors and can leak data (such as a password hash).

**Explanation:**

- Entities are for persistence. DTOs are for the API contract. They change for different reasons.
- Serialising an entity with a lazy collection triggers `LazyInitializationException` or an unwanted extra query (Q25).
- Bidirectional relations (`Order` <-> `OrderItem`) cause infinite JSON recursion.
- Request DTOs also prevent **mass assignment**: a client cannot set `id` or `role` by adding it to the JSON.
- Java `record`s are a good fit for DTOs. Map with MapStruct or by hand.

```java
public record OrderResponse(
    Long id,
    String status,
    BigDecimal total,
    List<OrderItemResponse> items
) {

    public static OrderResponse from(Order order) {
        return new OrderResponse(
            order.getId(),
            order.getStatus().name(),
            order.getTotal(),
            order.getItems()
                .stream()
                .map(OrderItemResponse::from)
                .toList()
        );
    }
}
```

**Follow-up questions:**

- **What is the cost of DTOs?**
  More classes and mapping code. MapStruct generates the mapping at compile time.
- **Can a DTO come straight from a query?**
  Yes, with a Spring Data projection or a constructor expression. This avoids loading full entities.

---

### Q19. How do you handle exceptions globally, and what is ProblemDetail? [Intermediate]

**Level: Intermediate**

**Short answer:** Use a `@RestControllerAdvice` class with `@ExceptionHandler` methods to turn exceptions into consistent error responses. `ProblemDetail` is Spring 6's built-in class for the standard error format (RFC 7807, updated by RFC 9457).

**Explanation:**

- One place maps domain exceptions to HTTP status and body. Controllers stay free of try/catch.
- `ProblemDetail` has `type`, `title`, `status`, `detail`, `instance` and custom properties. Content type is `application/problem+json`.
- **Boot 3:** set `spring.mvc.problemdetails.enabled=true` to get this format for standard Spring MVC exceptions. Extending `ResponseEntityExceptionHandler` gives the same effect inside your own advice.
- Never return stack traces or SQL errors to clients. Log them with a trace ID instead.

```java
@RestControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(OrderNotFoundException.class)
    public ProblemDetail handleNotFound(OrderNotFoundException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(
            HttpStatus.NOT_FOUND,
            ex.getMessage()
        );
        problem.setTitle("Order not found");
        problem.setProperty("orderId", ex.getOrderId());
        return problem;
    }

    @ExceptionHandler(Exception.class)
    public ProblemDetail handleUnexpected(Exception ex) {
        log.error("Unexpected error", ex);
        return ProblemDetail.forStatusAndDetail(
            HttpStatus.INTERNAL_SERVER_ERROR,
            "Unexpected error"
        );
    }
}
```

```json
{
  "type": "about:blank",
  "title": "Order not found",
  "status": 404,
  "detail": "Order 42 does not exist",
  "orderId": 42
}
```

**Follow-up questions:**

- **How does Spring choose between several `@ExceptionHandler`s?**
  The most specific exception type wins.
- **Does `@ControllerAdvice` catch exceptions from filters?**
  No. Filters run before the DispatcherServlet. Handle those in the filter or with an `AuthenticationEntryPoint` for security errors.
- **What is `@ResponseStatus`?**
  Puts a fixed status on an exception class. Simple, but less flexible than a handler.

---

### Q20. Filter vs HandlerInterceptor vs AOP: what is the difference? [Intermediate]

**Level: Intermediate**

**Short answer:** A servlet **Filter** works at the servlet level on raw request and response, before Spring MVC. A **HandlerInterceptor** works inside Spring MVC and knows which controller method will run. **AOP** wraps method calls on any Spring bean, with or without HTTP.

**Explanation:**

| | Filter | HandlerInterceptor | AOP (`@Aspect`) |
|---|---|---|---|
| Level | Servlet container | Spring MVC | Any bean method |
| Sees | Raw request/response | Request + handler method | Method arguments and result |
| Typical use | Security, CORS, request logging, trace IDs, compression | Auth checks per handler, timing, locale | Transactions, audit, metrics, retries |
| Order | First | After DispatcherServlet picks handler | Around bean calls |

```text
Request -> Filter -> DispatcherServlet -> Interceptor.preHandle -> Controller -> Service (AOP)
```

```java
@Component
public class TraceIdFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain chain
    ) throws ServletException, IOException {
        String traceId = UUID.randomUUID().toString();
        MDC.put("traceId", traceId);
        try {
            chain.doFilter(request, response);
        } finally {
            MDC.clear();
        }
    }
}
```

**Follow-up questions:**

- **Why `OncePerRequestFilter`?**
  It guarantees one execution per request, even with forwards or error dispatches.
- **How do you order filters?**
  `@Order` or `FilterRegistrationBean.setOrder`. Spring Security's filter chain has its own order.
- **Where would you put JWT checking?**
  In a filter inside the Spring Security chain (Q45), because security must run before MVC.

---

### Q21. How do you implement pagination? Offset vs keyset [Intermediate]

**Level: Intermediate**

**Short answer:** Use Spring Data's `Pageable` and return a `Page` or `Slice`. For very large tables use keyset (cursor) pagination, because offset pagination gets slower on deep pages.

**Explanation:**

- `Pageable` carries page number, size and sort. Spring MVC builds it from `?page=0&size=20&sort=createdAt,desc`.
- `Page<T>` runs an extra `COUNT` query to give total pages. `Slice<T>` skips it and only knows whether there is a next page, which is cheaper.
- **Offset:** `LIMIT 20 OFFSET 100000` makes the DB read and drop 100000 rows. Also, new inserts shift pages, so users see duplicates.
- **Keyset:** "give me the 20 orders after id 5000". Uses an index and has constant cost. You cannot jump to page N.
- Always cap `size` (`spring.data.web.pageable.max-page-size`, default 2000) and add a stable sort (for example by id).
- Do not return `Page` entities straight from the API. Map to a DTO. The JSON shape of the raw `PageImpl` class is not a stable contract, so prefer your own response type (newer Spring Data versions warn about this; check your version).

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    Page<Order> findByCustomerId(Long customerId, Pageable pageable);

    // Keyset style
    List<Order> findTop20ByIdGreaterThanOrderByIdAsc(Long lastSeenId);
}

@GetMapping
public Page<OrderResponse> list(
    @RequestParam Long customerId,
    @PageableDefault(size = 20, sort = "id") Pageable pageable
) {
    return orderRepository
        .findByCustomerId(customerId, pageable)
        .map(OrderResponse::from);
}
```

**Follow-up questions:**

- **Why is `Page` sometimes slow?**
  The count query on a large table can be heavy. Use `Slice` or a custom count query.
- **Pagination plus `JOIN FETCH` of a collection?**
  Hibernate paginates in memory and warns (HHH90003004). Use two queries: ids first, then fetch (Q26).

---

### Q22. How do you call other services: RestTemplate, RestClient, WebClient, OpenFeign? Timeouts? [Intermediate]

**Level: Intermediate**

**Short answer:** `RestClient` (Spring 6.1 / Boot 3.2) is the modern synchronous client with a fluent API. `RestTemplate` still works but is in maintenance mode. `WebClient` is the reactive, non-blocking client. OpenFeign generates a client from an interface. Always set connect and read timeouts.

**Explanation:**

| Client | Style | Notes |
|---|---|---|
| RestTemplate | Blocking | Older. Still widely used. No new features. |
| RestClient | Blocking, fluent | Boot 3.2+. Replaces RestTemplate in new code. |
| WebClient | Non-blocking | Needs `spring-boot-starter-webflux`. Good for streaming and many parallel calls. |
| OpenFeign | Declarative interface | Spring Cloud. Integrates with load balancer and Resilience4j. |
| HTTP Interface (`@HttpExchange`) | Declarative interface | Spring 6. Built on RestClient or WebClient. |

- **No default timeout** in many clients means a slow Inventory-Service can block all your Tomcat threads (Q68). Set both timeouts.
- Add a circuit breaker and retry for idempotent calls (Q52, Q53).
- **Version note:** Boot 3.4 added generic `spring.http.client.*` timeout properties. Earlier versions need code like below.

```java
@Bean
public RestClient inventoryRestClient(RestClient.Builder builder) {
    SimpleClientHttpRequestFactory factory = new SimpleClientHttpRequestFactory();
    factory.setConnectTimeout(2000);
    factory.setReadTimeout(5000);
    return builder
        .baseUrl("http://inventory-service")
        .requestFactory(factory)
        .build();
}

public StockResponse getStock(String sku) {
    return inventoryRestClient
        .get()
        .uri("/stock/{sku}", sku)
        .retrieve()
        .onStatus(HttpStatusCode::is5xxServerError, (req, res) -> {
            throw new InventoryUnavailableException(sku);
        })
        .body(StockResponse.class);
}
```

```java
// OpenFeign (Spring Cloud)
@FeignClient(name = "inventory-service")
public interface InventoryClient {

    @GetMapping("/stock/{sku}")
    StockResponse getStock(@PathVariable("sku") String sku);
}
```

**Follow-up questions:**

- **Feign default timeouts?**
  Feign has its own defaults (about 10s connect and 60s read in the default client). Set `spring.cloud.openfeign.client.config.default.*` explicitly.
- **Should you use WebClient inside a Spring MVC app?**
  You can, but calling `.block()` removes the benefit. Use RestClient for blocking code.
- **How do you pass the auth token or trace ID downstream?**
  Add a `ClientHttpRequestInterceptor` (RestClient) or a Feign `RequestInterceptor`. Auto-configured builders already propagate trace headers when Micrometer Tracing is on.

---

### Q23. How do you handle duplicate requests and make an API idempotent? [Advanced]

**Level: Advanced**

**Short answer:** An operation is idempotent if repeating it gives the same result as doing it once. For `POST /orders`, the client sends an `Idempotency-Key` header. The server stores the key with a unique constraint and returns the saved result when the same key arrives again.

**Explanation:**

- Duplicates happen because of client retries, timeouts, double clicks and gateway retries.
- GET, PUT and DELETE are idempotent by design. POST is not.
- Flow:
  1. Client generates a UUID and sends it with the request.
  2. Server tries to insert the key. A **unique constraint** makes the second concurrent insert fail. Do NOT do "select then insert" because two requests can both pass the select.
  3. Server processes the order, stores the response against the key.
  4. A repeat returns the stored response with the same status.
- Insert the key and the order in the SAME database transaction. Then a crash cannot leave a key without an order.
- Keys need an expiry (for example 24 hours) and a request hash so a reused key with a different body is rejected (422).

```java
@Entity
@Table(
    name = "idempotency_key",
    uniqueConstraints = @UniqueConstraint(columnNames = "key_value")
)
public class IdempotencyKey {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "key_value", nullable = false)
    private String keyValue;

    private String requestHash;

    @Column(length = 4000)
    private String responseJson;
}

@Transactional
public OrderResponse createOrder(String idempotencyKey, CreateOrderRequest request) {
    try {
        keyRepository.saveAndFlush(new IdempotencyKey(idempotencyKey, hash(request)));
    } catch (DataIntegrityViolationException duplicate) {
        // Key exists: return the stored response (or 409 if still processing)
        return loadStoredResponse(idempotencyKey);
    }
    Order order = orderRepository.save(Order.from(request));
    return OrderResponse.from(order);
}
// Note: after a unique-constraint failure the JPA transaction is marked rollback-only.
// In real code do the key insert in its own short transaction (REQUIRES_NEW) or use INSERT ... ON CONFLICT.
```

**Follow-up questions:**

- **Why is a Redis `SETNX` alone not enough?**
  Redis and the database are two systems. A crash between them can lose the key or the order. A DB unique constraint in the same transaction is safer. Redis is fine as a fast first check.
- **What about duplicate Kafka messages?**
  Same idea on the consumer side: store processed event IDs (Q56).
- **What is a natural idempotency key?**
  A business key such as `(customerId, cartId)`. A unique constraint on it stops duplicate orders without a header.

---

# Data and Transactions

---

### Q24. How does Spring Data JPA work? [Basic]

**Level: Basic**

**Short answer:** You declare a repository interface. Spring creates a proxy implementation at startup with ready-made CRUD methods, and builds queries from method names or `@Query`. JPA is the standard, Hibernate is the implementation that runs it.

**Explanation:**

- `JpaRepository<Order, Long>` gives `save`, `findById`, `findAll`, `deleteById`, paging and sorting.
- **Derived queries:** `findByCustomerIdAndStatus(...)` is parsed into JPQL.
- **`@Query`:** your own JPQL or native SQL.
- **Persistence context:** inside a transaction, Hibernate keeps loaded entities in a first-level cache. Changes to managed entities are saved automatically at flush or commit (**dirty checking**), even without calling `save`.
- Entity states: new (transient), managed, detached (after the transaction ends), removed.

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    List<Order> findByCustomerIdAndStatus(Long customerId, OrderStatus status);

    @Query("select o from Order o where o.total > :min")
    List<Order> findBigOrders(@Param("min") BigDecimal min);
}
```

```java
@Transactional
public void markPaid(Long id) {
    Order order = orderRepository.findById(id).orElseThrow();
    order.setStatus(OrderStatus.PAID);
    // No save() needed. Dirty checking runs UPDATE at commit.
}
```

**Follow-up questions:**

- **`save` vs `saveAndFlush`?**
  `save` may delay the SQL until flush or commit. `saveAndFlush` sends it immediately.
- **`getReferenceById` vs `findById`?**
  `getReferenceById` returns a lazy proxy without a query. `findById` queries at once.
- **`ddl-auto` in production?**
  Use `validate` or `none` and manage the schema with Flyway (Q31). Never `update`.

---

### Q25. Lazy vs eager loading, and what is LazyInitializationException? [Basic]

**Level: Basic**

**Short answer:** Lazy loading fetches related data only when you touch it. Eager loads it with the parent. `LazyInitializationException` happens when you touch a lazy relation after the transaction (and Hibernate session) has closed.

**Explanation:**

- Defaults in JPA: `@OneToMany` and `@ManyToMany` are LAZY. `@ManyToOne` and `@OneToOne` are EAGER.
- Best practice: make everything `LAZY` and fetch what you need per use case.
- Eager is not a fix. It loads data you may not need on every query and can cause N+1 (Q26).
- The exception appears when a controller or serializer reads `order.getItems()` after the service method returned.

```java
@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;

    @OneToMany(mappedBy = "order")
    private List<OrderItem> items = new ArrayList<>();
}

// Fails: the transaction ended in the service, items not loaded
Order order = orderService.find(1L);
order.getItems().size();
// LazyInitializationException: could not initialize proxy - no Session
```

Fixes, best first:

1. Load what you need in the query (`JOIN FETCH` or `@EntityGraph`, Q26).
2. Map to a DTO inside the `@Transactional` service method.
3. Use a projection that selects only the needed columns.
4. Avoid enabling open-in-view as a shortcut (Q30).

**Follow-up questions:**

- **Does `@Transactional(readOnly = true)` fix it?**
  No. The session still closes when the method returns.
- **Why not make everything EAGER?**
  Large object graphs, extra joins, and hidden N+1 queries.

---

### Q26. What is the N+1 problem and how do you fix it? [Intermediate]

**Level: Intermediate**

**Short answer:** N+1 means 1 query loads N parents and then N more queries load each parent's lazy children, one by one. Fix it by loading parents and children together: `JOIN FETCH`, `@EntityGraph`, batch fetching, or a DTO projection.

**Explanation:**

```java
List<Order> orders = orderRepository.findAll();
// 1 query: select * from orders

for (Order order : orders) {
    order.getItems().size();
    // N queries: select * from order_item where order_id = ?
}
// 100 orders = 101 queries
```

How to detect it:

- Turn on SQL logging in dev: `spring.jpa.show-sql=true` or logger `org.hibernate.SQL=DEBUG`.
- Enable Hibernate statistics (`hibernate.generate_statistics=true`) and look at statement counts.
- In tests, assert the query count with a library such as datasource-proxy.
- In production, look at slow endpoints with many identical DB spans in tracing.

Fixes:

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // 1. JOIN FETCH
    @Query("select distinct o from Order o join fetch o.items where o.status = :status")
    List<Order> findWithItems(@Param("status") OrderStatus status);

    // 2. EntityGraph
    @EntityGraph(attributePaths = {"items", "customer"})
    List<Order> findByStatus(OrderStatus status);
}
```

```yaml
# 3. Batch fetching: loads children for many parents with one "in (...)" query
spring:
  jpa:
    properties:
      hibernate:
        default_batch_fetch_size: 50
```

4. **DTO projection:** select only the columns you need (`select new ...` or an interface projection). Often the fastest for read APIs.

**Traps:**

- `JOIN FETCH` of a collection plus `Pageable` makes Hibernate paginate IN MEMORY (warning HHH90003004). Fix: first query a page of IDs, then fetch those with `JOIN FETCH`. Or use `default_batch_fetch_size`.
- Fetching two collections (`List`) at once causes `MultipleBagFetchException` and a Cartesian product. Fetch one by join and the other by batch size.
- Switching to `EAGER` does not remove N+1.

**Follow-up questions:**

- **JOIN FETCH vs EntityGraph?**
  Same effect. EntityGraph is declarative and reusable on repository methods. JOIN FETCH is explicit in the query.
- **Does N+1 happen with `@ManyToOne`?**
  Yes. If you access it for many rows, each customer can be loaded by a separate query.
- **Why `distinct` in the JOIN FETCH query?**
  A join repeats the parent for each child row. In Hibernate 6 duplicates are removed automatically, but `distinct` is harmless. (Version note: in Hibernate 5 you needed it.)

---

### Q27. How does @Transactional work, and what are the rollback rules? [Intermediate]

**Level: Intermediate**

**Short answer:** Spring wraps your bean in a proxy. The proxy opens a transaction before the method, commits when it returns, and rolls back if it throws. By default it rolls back only for unchecked exceptions (`RuntimeException` and `Error`), not for checked exceptions.

**Explanation:**

```text
caller -> [ Proxy: begin transaction ] -> real OrderService.placeOrder() -> [ Proxy: commit or rollback ]
```

- The transaction is bound to the current thread. All repository calls inside use the same connection.
- Put `@Transactional` on the service layer, not the controller or repository.
- `rollbackFor = Exception.class` makes checked exceptions roll back too.
- `readOnly = true` is a hint: Hibernate skips dirty checking and some drivers or routing setups use a replica. It does not stop writes by itself on every database.
- Keep transactions short. Do NOT call remote services or send Kafka messages inside a long transaction (see Q57, Q68).
- If an exception is caught and swallowed inside the method, the proxy sees a normal return and commits.

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final PaymentClient paymentClient;

    public OrderService(OrderRepository orderRepository, PaymentClient paymentClient) {
        this.orderRepository = orderRepository;
        this.paymentClient = paymentClient;
    }

    @Transactional(rollbackFor = Exception.class)
    public Order placeOrder(CreateOrderRequest request) throws PaymentException {
        Order order = orderRepository.save(Order.from(request));
        paymentClient.charge(order);
        // If charge() throws a checked PaymentException, rollbackFor makes it roll back.
        // Without rollbackFor, the order would be COMMITTED.
        return order;
    }
}
```

**Follow-up questions:**

- **Why does a checked exception not roll back by default?**
  It follows the EJB convention: checked exceptions are treated as business outcomes, not system failures.
- **Which annotation to use, `jakarta.transaction.Transactional` or Spring's?**
  Spring's `org.springframework.transaction.annotation.Transactional`. It has more options (`readOnly`, `timeout`).
- **What does `timeout` do?**
  Marks the transaction to fail after N seconds, checked at statement boundaries.
- **How do you do something only after commit?**
  `@TransactionalEventListener(phase = AFTER_COMMIT)`. Good for publishing events (Q57).

---

### Q28. Explain transaction propagation and isolation levels. [Intermediate]

**Level: Intermediate**

**Short answer:** Propagation says what happens when a transactional method is called while a transaction already exists. Default `REQUIRED` joins it. `REQUIRES_NEW` suspends it and starts a separate one. Isolation says how much a transaction sees of other transactions' uncommitted or newly committed changes.

**Explanation:**

| Propagation | Behaviour |
|---|---|
| REQUIRED (default) | Join the existing transaction, or create one |
| REQUIRES_NEW | Always create a new one. Suspend the outer one. |
| SUPPORTS | Join if one exists, otherwise run without |
| NOT_SUPPORTED | Run without a transaction. Suspend the outer one. |
| MANDATORY | Must already have one, otherwise throw |
| NEVER | Must NOT have one, otherwise throw |
| NESTED | Savepoint inside the outer one. Works mainly with JDBC. Check support for JPA. |

Important detail with `REQUIRED`: inner and outer share ONE transaction. If the inner method throws a runtime exception, the transaction is marked **rollback-only**. Even if the outer code catches the exception, the commit then fails with `UnexpectedRollbackException`.

Use `REQUIRES_NEW` for work that must survive an outer rollback, for example writing an audit record or a failure log. It uses a second DB connection, so overuse can exhaust the connection pool (Q34).

**Isolation levels** (what anomalies are allowed):

| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ_UNCOMMITTED | possible | possible | possible |
| READ_COMMITTED | no | possible | possible |
| REPEATABLE_READ | no | no | possible (MySQL InnoDB mostly prevents it) |
| SERIALIZABLE | no | no | no |

- `Isolation.DEFAULT` uses the database default. MySQL InnoDB defaults to REPEATABLE READ. PostgreSQL defaults to READ COMMITTED.
- Higher isolation means more locking or retries. Most apps stay on the default and use locking (Q32) for hot rows.

```java
@Service
public class AuditService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logFailure(Long orderId, String reason) {
        // Commits on its own, even if the caller's transaction rolls back
    }
}
```

**Follow-up questions:**

- **Dirty read vs non-repeatable read vs phantom read?**
  Dirty: you read uncommitted data. Non-repeatable: the same row read twice gives different values. Phantom: the same query returns different rows.
- **What is `UnexpectedRollbackException`?**
  The outer transaction tried to commit, but an inner `REQUIRED` call already marked it rollback-only.
- **Is `@Transactional` enough to stop two users buying the last item?**
  No. Use a version column or a locking read (Q32), or an atomic update.

---

### Q29. Why does @Transactional not work with self-invocation or on private methods? [Intermediate]

**Level: Intermediate**

**Short answer:** Transactions are applied by a proxy. A call from inside the same class (`this.method()`) skips the proxy, so no transaction starts. Private methods cannot be intercepted at all.

**Explanation:**

```text
Outside call:   controller -> [Proxy] -> OrderService.placeOrder()   (transaction works)
Self call:      OrderService.placeOrder() -> this.saveAudit()         (no proxy, annotation ignored)
```

Cases where `@Transactional` is silently ignored:

- Self-invocation: method A calls `@Transactional` method B in the same class.
- `private` methods. Also `final` methods and classes with CGLIB proxies.
- **Version note:** since Spring 6.0, class-based (CGLIB) proxies also support `protected` and package-visible methods. Keep annotated methods `public` to stay safe.
- Bean not created by Spring (`new OrderService()`).
- Checked exception without `rollbackFor`.
- Exception swallowed with try/catch.
- Wrong `@Transactional` import, or use inside `@PostConstruct` before the proxy exists (Q8).

```java
@Service
public class OrderService {

    public void process(Long id) {
        // Self call: no new transaction here
        this.saveAudit(id);
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void saveAudit(Long id) {
    }
}
```

Fixes:

1. Move the method to another bean and inject it (cleanest).
2. Use `TransactionTemplate` for programmatic control.
3. Inject the bean's own proxy (`@Lazy` self-injection). Works, but is a design smell.
4. AspectJ weaving mode (`@EnableTransactionManagement(mode = ASPECTJ)`). Rarely used.

```java
@Service
public class OrderService {

    private final TransactionTemplate transactionTemplate;

    public OrderService(PlatformTransactionManager transactionManager) {
        this.transactionTemplate = new TransactionTemplate(transactionManager);
    }

    public void process(Long id) {
        transactionTemplate.executeWithoutResult(status -> {
            // runs inside a transaction
        });
    }
}
```

**Follow-up questions:**

- **Does the same limit apply to `@Async`, `@Cacheable`, `@PreAuthorize`?**
  Yes. They are all proxy-based (Q35).
- **How would you notice this bug?**
  Data is not rolled back as expected. Turn on `logging.level.org.springframework.transaction=DEBUG` to see transaction begin and commit.

---

### Q30. What is open-in-view and should you turn it off? [Intermediate]

**Level: Intermediate**

**Short answer:** Open-in-view (OSIV) keeps the JPA session open for the whole web request, so lazy loading works in controllers and during JSON writing. Boot enables it by default and logs a warning. For production APIs, set `spring.jpa.open-in-view=false`.

**Explanation:**

- With OSIV on, a lazy load in the view or controller works, but each lazy load runs a query outside any planned transaction.
- It can hold a database connection for the whole request time, including time spent serialising the response. This drains the connection pool under load (Q34).
- It hides N+1 problems and hides missing fetch plans, so bugs show up late.
- With it off, you must fetch everything the response needs inside the service method. This is what we want.

```yaml
spring:
  jpa:
    open-in-view: false
```

**Follow-up questions:**

- **What breaks when you turn it off?**
  Code that reads lazy relations after the service returns gets `LazyInitializationException`. Fix with DTO mapping in the service, `JOIN FETCH` or `@EntityGraph`.
- **Does it apply to Kafka listeners or scheduled jobs?**
  No. OSIV is a web-request feature.

---

### Q31. How do you manage schema changes with Flyway? [Intermediate]

**Level: Intermediate**

**Short answer:** Flyway runs versioned SQL scripts at startup in order and records them in a history table, so every environment gets the same schema. Boot auto-configures it when `flyway-core` is on the classpath.

**Explanation:**

- Scripts live in `src/main/resources/db/migration`, named `V1__create_orders.sql`, `V2__add_status.sql`.
- Flyway stores applied versions and checksums in `flyway_schema_history`. If you edit an applied script, startup fails with a checksum error. Add a new version instead.
- **Version note:** for MySQL, newer Flyway versions need the extra `flyway-mysql` dependency (Flyway 10, managed by Boot 3.3+).
- Set `spring.jpa.hibernate.ddl-auto=validate` so Hibernate only checks the schema.
- **Zero-downtime changes** use "expand and contract": add the new column (nullable), deploy code that writes both, backfill, switch reads, then remove the old column in a later release. Old and new app versions run at the same time during a rolling deploy, so the schema must work for both.
- With many instances, Flyway locks so only one runs migrations. Slow migrations delay startup and can fail readiness (Q69).

```text
db/migration/
  V1__create_orders.sql
  V2__add_order_status.sql
  V3__add_idempotency_key_table.sql
```

```sql
-- V2__add_order_status.sql
ALTER TABLE orders
    ADD COLUMN status VARCHAR(20) NOT NULL DEFAULT 'NEW';
```

**Follow-up questions:**

- **Flyway vs Liquibase?**
  Flyway uses plain SQL files and is simpler. Liquibase uses XML/YAML changelogs, supports rollback and is database independent.
- **Can you roll back a migration?**
  Flyway Community has no automatic undo. Write a new forward migration.
- **Run migrations on startup or as a separate step?**
  A separate step (init container or pipeline job) avoids slow startup and race conditions.

---

### Q32. How do optimistic and pessimistic locking work? [Advanced]

**Level: Advanced**

**Short answer:** Optimistic locking assumes conflicts are rare. A `@Version` column is checked on update, and a concurrent change makes one writer fail. Pessimistic locking locks the row in the database (`SELECT ... FOR UPDATE`) so others wait.

**Explanation:**

- **Optimistic (`@Version`):** Hibernate runs `UPDATE ... WHERE id = ? AND version = ?`. If 0 rows are updated, someone else changed it first, and you get `ObjectOptimisticLockingFailureException`. Then you retry or return 409.
- No database lock is held, so it scales well. Best for read-heavy data with few conflicts, and for user edits.
- **Pessimistic:** `@Lock(LockModeType.PESSIMISTIC_WRITE)`. Other transactions wait. Use it for hot rows with high contention (stock counters), and keep the transaction short. Risk: deadlocks and waiting threads. Set a lock timeout.
- A third option: an atomic update in SQL, which often needs no lock at all.

```java
@Entity
public class InventoryItem {

    @Id
    private Long id;

    private int available;

    @Version
    private long version;
}

// Optimistic: retry on conflict (Spring Retry)
@Retryable(retryFor = ObjectOptimisticLockingFailureException.class, maxAttempts = 3)
@Transactional
public void reserve(Long itemId, int quantity) {
    InventoryItem item = repository.findById(itemId).orElseThrow();
    item.setAvailable(item.getAvailable() - quantity);
}
```

```java
// Pessimistic
public interface InventoryRepository extends JpaRepository<InventoryItem, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("select i from InventoryItem i where i.id = :id")
    Optional<InventoryItem> findForUpdate(@Param("id") Long id);
}

// Atomic update: no lock and no race
// @Modifying
// @Query("update InventoryItem i set i.available = i.available - :q where i.id = :id and i.available >= :q")
// int reserve(@Param("id") Long id, @Param("q") int quantity);   // returns 0 if not enough stock
```

**Follow-up questions:**

- **Where must the retry sit?**
  OUTSIDE the transaction. A retry inside the same failed transaction does not help. So the retry proxy must wrap the transactional proxy (check the advice order, or retry from the caller).
- **What causes a deadlock and how do you reduce it?**
  Two transactions lock rows in opposite order. Lock in a fixed order, keep transactions short, use lock timeouts.
- **Does `@Version` work across multiple service instances?**
  Yes. The check is in the database.

---

### Q33. How do you do fast batch inserts with JPA? [Advanced]

**Level: Advanced**

**Short answer:** Turn on JDBC batching (`hibernate.jdbc.batch_size`) and use a generation strategy that allows it. `GenerationType.IDENTITY` disables Hibernate insert batching, so use a SEQUENCE with a pooled `allocationSize`, or use `JdbcTemplate.batchUpdate` for bulk loads.

**Explanation:**

- Plain `saveAll` on 10,000 rows can send 10,000 separate inserts.
- With IDENTITY (MySQL `AUTO_INCREMENT`), Hibernate must run each insert at once to get the ID, so it cannot batch. MySQL has no sequences. Options: `JdbcTemplate` batch, a table-based generator, or generate IDs yourself (UUID / snowflake).
- The settings below are Hibernate property names. Check them for your version.
- For MySQL Connector/J, add `rewriteBatchedStatements=true` to the JDBC URL so it rewrites many inserts into one multi-row statement.
- The persistence context grows with each saved entity. For very large loads, `flush()` and `clear()` every batch.

```yaml
spring:
  datasource:
    url: jdbc:mysql://mysql:3306/orders?rewriteBatchedStatements=true
  jpa:
    properties:
      hibernate:
        jdbc:
          batch_size: 50
        order_inserts: true
        order_updates: true
```

```java
public void importOrders(List<Order> orders) {
    jdbcTemplate.batchUpdate(
        "insert into orders (customer_id, status, total) values (?, ?, ?)",
        orders,
        500,
        (ps, order) -> {
            ps.setLong(1, order.getCustomerId());
            ps.setString(2, order.getStatus().name());
            ps.setBigDecimal(3, order.getTotal());
        }
    );
}
```

**Follow-up questions:**

- **How do you confirm batching works?**
  Enable Hibernate statistics and check "JDBC batches executed", or look at the DB general log.
- **Batch updates and deletes?**
  `order_updates` helps updates. For bulk changes, a single `@Modifying @Query` update/delete is faster than loading entities, but it bypasses the persistence context and `@Version`.

---

### Q34. How do you tune HikariCP and diagnose "connection is not available"? [Advanced]

**Level: Advanced**

**Short answer:** HikariCP is Boot's default connection pool. The default `maximumPoolSize` is 10. The error "Connection is not available, request timed out after 30000ms" means all connections were busy for too long, usually because of slow queries, long transactions, leaks or a pool that is too small.

**Explanation:**

- A pool keeps a fixed set of open DB connections so requests do not pay connection setup cost.
- A small pool is usually better than a big one. A common starting point is `(cores * 2) + disk spindles`, then measure. More connections than the DB can run in parallel only adds waiting.
- Total connections = pool size x number of instances. This must stay below the database `max_connections`. With 20 pods x 10 = 200 connections, a MySQL default of 151 will fail.
- Keep `maxLifetime` shorter than the database or proxy idle timeout (for example MySQL `wait_timeout`), so Hikari retires connections before the server closes them.

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 20
      connection-timeout: 3000
      max-lifetime: 1500000
      leak-detection-threshold: 20000
      pool-name: orders-pool
```

Common causes of exhaustion and fixes:

| Cause | Fix |
|---|---|
| Slow query, missing index | Slow query log, `EXPLAIN`, add index |
| Long transaction (remote call inside `@Transactional`) | Move the call outside the transaction |
| Open-in-view holding connections | `open-in-view=false` (Q30) |
| `REQUIRES_NEW` nesting (needs 2 connections per request) | Redesign, or enlarge the pool |
| Connection leak (not returned) | `leak-detection-threshold` shows the stack trace |
| Pool too small for traffic | Increase carefully, check DB capacity first |

Watch metrics: `hikaricp_connections_active`, `hikaricp_connections_pending`, `hikaricp_connections_timeout_total` (Q64).

**Follow-up questions:**

- **Why lower `connection-timeout` from 30 seconds?**
  Fail fast. With 30s, requests pile up and use all Tomcat threads while they wait.
- **Does a bigger pool fix slow queries?**
  No. It usually makes the database slower.
- **How do virtual threads change this?**
  Many more threads can wait for connections, so the pool becomes the main limit. Size and timeouts matter even more.

# AOP, Proxies, Caching and Async

---

### Q35. What is AOP and where does Spring use it? [Basic]

**Level: Basic**

**Short answer:** AOP (Aspect-Oriented Programming) lets you add behaviour around method calls, such as transactions or logging, without changing the method code. Spring does it with proxies: an object that wraps your bean and runs extra code before and after the real method.

**Explanation:**

- Some concerns cut across many classes: transactions, security, caching, logging, metrics. AOP puts them in one place.
- Terms:
  - **Aspect:** a class holding the cross-cutting code.
  - **Advice:** the code (`@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around`).
  - **Pointcut:** an expression that says which methods to wrap.
  - **Join point:** the method call being intercepted. In Spring AOP it is always a method execution on a Spring bean.
- Spring features built on AOP proxies: `@Transactional`, `@Cacheable`, `@Async`, `@PreAuthorize`, `@Retryable`, `@Validated` on methods, Resilience4j annotations.

```text
caller -> [ Proxy (runs advice) ] -> real bean method
```

**Follow-up questions:**

- **How does Spring create the proxy?**
  A `BeanPostProcessor` wraps the bean after initialisation (Q8). Callers receive the proxy, not the raw object.
- **Spring AOP vs AspectJ?**
  Spring AOP is proxy-based and works only on Spring beans' public method calls from outside. AspectJ weaves bytecode, so it can intercept field access, constructors and self-calls. It needs a compiler or agent.

---

### Q36. JDK dynamic proxy vs CGLIB: what is the difference? [Intermediate]

**Level: Intermediate**

**Short answer:** A JDK proxy implements the same interfaces as the target, so it only works when the bean has an interface. CGLIB creates a SUBCLASS of the target class, so it works without interfaces but cannot proxy `final` classes or methods. Spring Boot uses CGLIB by default.

**Explanation:**

| | JDK dynamic proxy | CGLIB |
|---|---|---|
| Needs interface | Yes | No |
| How | Implements the interfaces | Subclass of the class |
| Limits | Inject by interface only | No `final` classes or methods; needs a usable constructor |
| Boot default | No | Yes (`spring.aop.proxy-target-class=true`) |

- Boot 2.0+ defaults to CGLIB even when interfaces exist, so you can inject the concrete class without errors.
- A `final` method on a CGLIB-proxied class runs on the proxy object itself with uninitialised fields, which causes odd null pointer errors. Kotlin classes are `final` by default, so use the `kotlin-spring` plugin.
- Proxy limits that apply to both: self-invocation skips the proxy (Q29), and only the outermost call is intercepted.
- Check what you have: `AopUtils.isAopProxy(bean)`, `AopUtils.isCglibProxy(bean)`, or print `bean.getClass()` (it shows `OrderService$$SpringCGLIB$$0`).

**Follow-up questions:**

- **Why can't you proxy a final class?**
  CGLIB must extend it. Java forbids extending a `final` class.
- **What is the order of several advices on one method?**
  Controlled with `@Order` on aspects. Lower number runs first on the way in (outermost).
- **Does a proxy work on `this` inside the target?**
  No. Inside the target, `this` is the real object, not the proxy.

---

### Q37. What are good uses of @Aspect, and how do you write one? [Intermediate]

**Level: Intermediate**

**Short answer:** Use an aspect for behaviour that applies to many methods and is not business logic: timing, audit logging, metrics, retry, simple authorisation checks. Use `@Around` advice for full control of the call.

**Explanation:**

- Add `spring-boot-starter-aop`. Boot enables AspectJ-style annotation support.
- Prefer a custom annotation as the pointcut. It is explicit and easy to find in the code.
- With `@Around` you must call `joinPoint.proceed()` and return its result, otherwise the target never runs.
- Do not put heavy logic or remote calls in an aspect. It runs on every call.
- Be careful with the order against `@Transactional`: use `@Order` if your aspect must run inside or outside the transaction.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Timed {
}

@Aspect
@Component
public class TimingAspect {

    private static final Logger log = LoggerFactory.getLogger(TimingAspect.class);

    @Around("@annotation(com.example.Timed)")
    public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.nanoTime();
        try {
            return joinPoint.proceed();
        } finally {
            long millis = (System.nanoTime() - start) / 1_000_000;
            log.info("{} took {} ms", joinPoint.getSignature().toShortString(), millis);
        }
    }
}

@Service
public class OrderService {

    @Timed
    public Order find(Long id) {
        return null;
    }
}
```

**Follow-up questions:**

- **`@Around` vs `@Before` / `@After`?**
  `@Before` and `@After` cannot change the result or stop the call. `@Around` can do both.
- **Pointcut expression `execution(* com.example.order..*Service.*(..))`: what does it mean?**
  Any return type, any method, in classes ending with `Service` in the `com.example.order` package and its sub-packages, with any arguments.
- **Why use Micrometer's `@Timed`/`@Observed` instead of custom timing?**
  They publish to Prometheus and tracing for free. Write your own only for special needs.

---

### Q38. How does caching work with @Cacheable and Redis? [Intermediate]

**Level: Intermediate**

**Short answer:** `@Cacheable` stores a method's result under a key and returns it next time without running the method. It is proxy-based like `@Transactional`. With Redis, the cache is shared by all instances of a service.

**Explanation:**

- Enable with `@EnableCaching` and add `spring-boot-starter-cache` and `spring-boot-starter-data-redis`. Set `spring.cache.type=redis`.
- Annotations:
  - `@Cacheable`: read from cache, or run the method and store.
  - `@CachePut`: always run the method and update the cache.
  - `@CacheEvict`: remove entries (on update or delete).
- The default key is built from the method arguments. Set `key` and `condition` with SpEL when needed.
- Set a **TTL** (time to live) for every cache. Redis does not expire entries unless you tell it to.
- The default value serializer is JDK serialization. Prefer JSON, so values are readable and survive class changes. Cached classes must be serialisable either way.
- A local cache (Caffeine) is faster but each instance has its own copy and they can disagree. Redis is shared but costs a network call. Some teams use both (two-level).
- Self-invocation and private methods skip the cache (Q29).

```java
@Service
public class ProductService {

    @Cacheable(cacheNames = "products", key = "#sku", unless = "#result == null")
    public Product getProduct(String sku) {
        // runs only on a cache miss
        return productRepository.findBySku(sku).orElse(null);
    }

    @CacheEvict(cacheNames = "products", key = "#product.sku")
    public void update(Product product) {
        productRepository.save(product);
    }
}
```

```java
@Bean
public RedisCacheManager cacheManager(RedisConnectionFactory factory) {
    RedisCacheConfiguration config = RedisCacheConfiguration
        .defaultCacheConfig()
        .entryTtl(Duration.ofMinutes(10))
        .serializeValuesWith(
            RedisSerializationContext.SerializationPair.fromSerializer(
                new GenericJackson2JsonRedisSerializer()
            )
        );
    return RedisCacheManager
        .builder(factory)
        .cacheDefaults(config)
        .build();
}
```

**Follow-up questions:**

- **What should you NOT cache?**
  Data that must always be fresh (account balance, stock for checkout), per-user private data under shared keys, or huge objects.
- **What if Redis is down?**
  By default the call throws. Configure a `CacheErrorHandler` so cache errors are logged and the method runs normally.
- **Why `unless = "#result == null"`?**
  To avoid caching "not found" results by mistake. (Sometimes you cache nulls on purpose, see Q41.)

---

### Q39. How does @Async work, and how do you configure its thread pool? [Intermediate]

**Level: Intermediate**

**Short answer:** `@Async` makes a method run on another thread, through a proxy. Add `@EnableAsync`, and return `void` or `CompletableFuture`. Always configure the executor, because the defaults are not tuned and an unbounded queue can hide overload.

**Explanation:**

- Without Boot, Spring defaults to `SimpleAsyncTaskExecutor`, which starts a NEW thread per task (no pool). Boot auto-configures a `ThreadPoolTaskExecutor` named `applicationTaskExecutor` with core size 8 and an UNBOUNDED queue (check your version). Under load, tasks wait in memory and can cause an OutOfMemoryError.
- Set core size, max size, queue capacity and a rejection policy. Size from the work: IO-bound work can have more threads than CPU cores.
- **Context is not propagated.** The new thread has no `SecurityContext`, no MDC (log trace ID) and no transaction. Use a `TaskDecorator` to copy them.
- Exceptions in a `void` `@Async` method are not seen by the caller. Handle them with `AsyncUncaughtExceptionHandler`, or return `CompletableFuture` and handle errors there.
- `@Async` is proxy-based, so self-invocation does not work (Q29).
- Use it for work the response does not need, such as sending an email. It is NOT reliable for important work: if the app stops, queued tasks are lost. Use Kafka or an outbox for that (Q57).

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean("mailExecutor")
    public Executor mailExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(4);
        executor.setMaxPoolSize(8);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("mail-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.setTaskDecorator(new MdcTaskDecorator());
        executor.initialize();
        return executor;
    }
}

@Service
public class NotificationService {

    @Async("mailExecutor")
    public CompletableFuture<Void> sendConfirmation(Long orderId) {
        // runs on a "mail-" thread
        return CompletableFuture.completedFuture(null);
    }
}
```

**Follow-up questions:**

- **Why does the executor only grow to max size after the queue is full?**
  A `ThreadPoolExecutor` adds threads beyond core only when the queue is full. With an unbounded queue, max size is never used.
- **How do virtual threads change this?**
  With Java 21 and `spring.threads.virtual.enabled=true`, Boot can use virtual threads for the task executor. Pool sizing matters less, but downstream limits (DB, HTTP) still matter.
- **What does `CallerRunsPolicy` do?**
  When the pool is full, the calling thread runs the task. It slows the caller down, which is a simple form of back-pressure.

---

### Q40. How do you run @Scheduled jobs when there are several instances? [Advanced]

**Level: Advanced**

**Short answer:** `@Scheduled` runs on EVERY instance, so with 3 pods the job runs 3 times. Use a distributed lock such as ShedLock, a Kubernetes CronJob, or a clustered scheduler like Quartz so only one instance runs it.

**Explanation:**

- Add `@EnableScheduling`. By default, scheduled tasks share a single thread, so one slow job delays the others. Set `spring.task.scheduling.pool.size` or use a custom scheduler.
- **ShedLock:** before the job runs, it tries to insert or update a lock row in a shared store (database or Redis). Only the instance that gets the lock runs the job.
- Set `lockAtMostFor` (safety if the instance dies) and `lockAtLeastFor` (stop quick repeats because of clock difference between pods).
- Alternatives: a Kubernetes `CronJob` that runs a separate pod (clean, but needs separate packaging), or Quartz with JDBC job store.
- Make jobs **idempotent** anyway. A lock does not give exactly-once if a job crashes halfway.

```java
@Component
public class OutboxCleanupJob {

    @Scheduled(cron = "0 0 * * * *")
    @SchedulerLock(
        name = "outboxCleanup",
        lockAtMostFor = "PT10M",
        lockAtLeastFor = "PT1M"
    )
    public void cleanup() {
        // delete processed outbox rows older than 7 days
    }
}
```

**Follow-up questions:**

- **`fixedRate` vs `fixedDelay` vs `cron`?**
  `fixedRate`: start every N ms. `fixedDelay`: wait N ms after the previous run ends. `cron`: calendar schedule.
- **What if the job runs longer than the interval?**
  With a single scheduler thread, runs queue up. With a distributed lock, the next run is skipped while locked.
- **Why not use a leader election?**
  It also works (for example with Kubernetes leases). ShedLock is simpler for ordinary jobs.

---

### Q41. What cache problems should you know: stampede, penetration, avalanche, stale data? [Advanced]

**Level: Advanced**

**Short answer:** Stampede: many requests rebuild the same expired key at once. Penetration: requests for keys that never exist always hit the database. Avalanche: many keys expire at the same time. Stale data: the cache holds old values after the database changed.

**Explanation:**

| Problem | What happens | Fix |
|---|---|---|
| Stampede (hot key) | A popular key expires. 1000 requests all query the DB. | `@Cacheable(sync = true)` (one thread rebuilds, others wait), a lock per key, refresh before expiry |
| Penetration | Attackers or bugs request ids that do not exist. Every request reaches the DB. | Cache null results for a short time, Bloom filter, validate input, rate limit |
| Avalanche | Many keys set with the same TTL expire together. | Add random jitter to TTL, warm up the cache, circuit breaker on the DB |
| Stale data | Update in DB but cache not evicted, or race between two writers | Evict on write, short TTL, publish an invalidation event, accept eventual consistency |

- `sync = true` works per instance only. Across many instances, each instance may still rebuild once. Often that is acceptable.
- The classic **cache-aside** pattern: read cache, on a miss read DB and fill cache. On write, update the DB and then EVICT the cache entry (do not update the cache in a racy way).
- A cache is an optimisation. The system must still work if it is empty. Test that Redis loss does not take the service down.

```java
@Cacheable(cacheNames = "products", key = "#sku", sync = true)
public Product getProduct(String sku) {
    // only one thread per key runs this on a miss
    return productRepository.findBySku(sku).orElseThrow();
}
```

**Follow-up questions:**

- **Why evict instead of update the cache on write?**
  Two concurrent updates can write to the cache in a different order than the DB, leaving the cache wrong. Evicting is safer.
- **What cache hit ratio is good?**
  It depends on the data. Watch the metric (`cache.gets` with `result=hit/miss`) and the DB load, not a fixed number.
- **How do you invalidate across services?**
  Publish an event (Kafka) and let each service evict its own copy.

---

# Security

---

### Q42. How does Spring Security work? Filter chain, authentication vs authorization [Basic]

**Level: Basic**

**Short answer:** Spring Security is a chain of servlet filters in front of your controllers. **Authentication** answers "who are you?". **Authorization** answers "are you allowed to do this?". After authentication, the result is stored in the `SecurityContext`.

**Explanation:**

```text
Request
  -> DelegatingFilterProxy -> FilterChainProxy -> SecurityFilterChain (list of filters)
        |-- CorsFilter
        |-- CsrfFilter
        |-- (your JWT filter / UsernamePasswordAuthenticationFilter)
        |-- ExceptionTranslationFilter   (turns errors into 401/403)
        |-- AuthorizationFilter          (checks URL rules)
  -> DispatcherServlet -> Controller
```

- **Authentication object:** holds the principal (user), credentials and authorities (roles and permissions).
- **AuthenticationManager** delegates to **AuthenticationProvider**s. The common one, `DaoAuthenticationProvider`, loads a user with `UserDetailsService` and checks the password with a `PasswordEncoder`.
- **SecurityContextHolder** stores the `SecurityContext` in a `ThreadLocal`, so any code on the same thread can read the current user.
- **Status codes:** 401 Unauthorized = not authenticated (no or bad credentials). 403 Forbidden = authenticated but not allowed.
- Roles are authorities with the `ROLE_` prefix. `hasRole("ADMIN")` checks the authority `ROLE_ADMIN`.

```java
Authentication authentication = SecurityContextHolder
    .getContext()
    .getAuthentication();
String username = authentication.getName();
```

**Follow-up questions:**

- **What happens to the SecurityContext in `@Async` code?**
  It is not copied by default. Use `DelegatingSecurityContextExecutor` or a `TaskDecorator`.
- **401 vs 403?**
  401: I do not know who you are. 403: I know who you are, but you may not do this.
- **Is session state used?**
  In a stateless REST API with JWT you set `SessionCreationPolicy.STATELESS`, so no HTTP session stores the context.

---

### Q43. How should passwords be stored? [Basic]

**Level: Basic**

**Short answer:** Never store plain passwords or fast hashes like MD5 or SHA-256. Store a salted, slow, adaptive hash. Use `BCryptPasswordEncoder` or the `DelegatingPasswordEncoder` from `PasswordEncoderFactories`.

**Explanation:**

- **Hashing** is one way: you cannot get the password back. You hash the login attempt and compare.
- **Salt:** random data added before hashing, so equal passwords give different hashes and rainbow tables fail. BCrypt adds and stores the salt in its output.
- **Work factor (cost):** BCrypt is deliberately slow, so brute force is expensive. Raise the cost over time.
- `DelegatingPasswordEncoder` stores a prefix like `{bcrypt}` in the hash. Then you can upgrade the algorithm later and still verify old hashes. Argon2 and scrypt are alternatives.
- Do not log passwords. Use `char[]` or short-lived variables where practical. Add rate limiting and lockout on login.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder(12);
}

// Register
String hash = passwordEncoder.encode(rawPassword);
// e.g. $2a$12$... (salt and hash in one string)

// Login check
boolean matches = passwordEncoder.matches(rawPassword, hash);
```

**Follow-up questions:**

- **Why not encrypt passwords?**
  Encryption is reversible. If the key leaks, all passwords leak.
- **Why is SHA-256 not enough?**
  It is very fast, so attackers can try billions of guesses per second on a GPU.
- **How do you upgrade the cost factor?**
  After a successful login, check `passwordEncoder.upgradeEncoding(hash)` and store the new hash.

---

### Q44. How do you configure Spring Security 6 with a SecurityFilterChain? [Intermediate]

**Level: Intermediate**

**Short answer:** In Boot 3 (Spring Security 6) you define a `SecurityFilterChain` bean and configure `HttpSecurity` with the lambda DSL. `WebSecurityConfigurerAdapter` was removed. Use `authorizeHttpRequests` and `requestMatchers`.

**Explanation:**

- **Version note:** Boot 2.x / Security 5 used `WebSecurityConfigurerAdapter`, `antMatchers` and `authorizeRequests`. In Boot 3 use `SecurityFilterChain`, `requestMatchers` and `authorizeHttpRequests`. The old methods are removed.
- Rules are checked in order. The FIRST match wins, so put specific rules before general ones. End with `anyRequest().authenticated()` (deny by default).
- For a REST API with JWT: stateless session, CSRF disabled (reason in Q46), custom filter before `UsernamePasswordAuthenticationFilter`.
- Return JSON for 401/403 with a custom `AuthenticationEntryPoint` and `AccessDeniedHandler`. Otherwise you may get an HTML page or an empty body.

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
            .cors(Customizer.withDefaults())
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/actuator/health/**").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/products/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
            .exceptionHandling(ex -> ex
                .authenticationEntryPoint(new Http401EntryPoint())
            );
        return http.build();
    }
}
```

**Follow-up questions:**

- **Order of matchers matters. Why?**
  The first matching rule decides. If `anyRequest().authenticated()` came first, later `permitAll()` rules would never apply.
- **Can you have more than one SecurityFilterChain?**
  Yes. Use `securityMatcher("/api/**")` on each and `@Order`. For example one chain for the API and one for actuator.
- **Does `permitAll` skip the filters?**
  No. The request still goes through the whole chain (including your JWT filter). `permitAll` only means no authentication is required. Skipping the chain with `web.ignoring()` is discouraged because it also skips security headers.

---

### Q45. Walk through a JWT authentication flow in Spring Boot. [Intermediate]

**Level: Intermediate**

**Short answer:** The user logs in with credentials. The server verifies them and returns a signed JWT. On every later request, the client sends `Authorization: Bearer <token>`. A filter validates the signature and expiry, builds an `Authentication` and puts it in the `SecurityContext`.

**Explanation:**

A **JWT** (JSON Web Token) has three Base64 parts: `header.payload.signature`. The payload has **claims** such as `sub` (user id), `exp` (expiry), `iss` (issuer), `roles`. The payload is only encoded, NOT encrypted. Anyone can read it. The signature proves it was not changed.

```text
1. POST /api/auth/login  {email, password}
2. Server: AuthenticationManager checks password -> creates JWT (signed) -> returns it
3. Client stores token. Each request:  Authorization: Bearer eyJhbGciOi...
4. JwtAuthenticationFilter: parse + verify signature + check exp
        -> set SecurityContext
5. AuthorizationFilter checks roles -> Controller
```

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService, UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain chain
    ) throws ServletException, IOException {
        String header = request.getHeader("Authorization");
        if (header == null || !header.startsWith("Bearer ")) {
            chain.doFilter(request, response);
            return;
        }
        String token = header.substring(7);
        try {
            String username = jwtService.validateAndGetSubject(token);
            // throws if signature is wrong or token is expired
            UserDetails user = userDetailsService.loadUserByUsername(username);
            UsernamePasswordAuthenticationToken authentication =
                new UsernamePasswordAuthenticationToken(user, null, user.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(authentication);
        } catch (JwtException ex) {
            SecurityContextHolder.clearContext();
            // continue: the authorization step will answer 401
        }
        chain.doFilter(request, response);
    }
}
```

- The `JwtService` wraps a library (jjwt, or Nimbus via Spring's `NimbusJwtDecoder`). Do not write your own crypto.
- Because the token is self-contained, services do not need a session store. That is why JWT fits microservices.
- Prefer the built-in **OAuth2 Resource Server** support (Q49) over hand-written filters when you can.

**Follow-up questions:**

- **HS256 vs RS256?**
  HS256 uses one shared secret to sign and verify, so every service that verifies could also forge tokens. RS256 signs with a private key and verifies with a public key. Better for microservices.
- **Do you load the user from the DB on every request?**
  Not required. You can build the `Authentication` from claims only, which is faster. The cost is that role changes only apply after the token expires.
- **Where do roles come from?**
  A `roles` claim, mapped to `GrantedAuthority` objects.

---

### Q46. What is the difference between CORS and CSRF? [Intermediate]

**Level: Intermediate**

**Short answer:** CORS is a browser rule that blocks a web page from calling a different origin unless the server allows it. CSRF is an attack where another site makes the user's browser send an authenticated request using cookies. CORS does not stop CSRF.

**Explanation:**

- **Origin** = scheme + host + port. `http://localhost:3000` and `http://localhost:8080` are different origins.
- **CORS** (Cross-Origin Resource Sharing): the server sends headers (`Access-Control-Allow-Origin`) to say which origins may read responses. Browsers send a **preflight** `OPTIONS` request first for non-simple requests (for example with an `Authorization` header). CORS is enforced by browsers only. Postman and curl ignore it.
- **CSRF** (Cross-Site Request Forgery): works because browsers attach cookies automatically. If login uses a session cookie, a malicious page can post a form to your API and the cookie goes along. Defence: CSRF token, `SameSite` cookies.
- If your API uses a **bearer token in the `Authorization` header** (not a cookie) and no session, the browser does not attach it automatically. CSRF protection is not needed, so disabling CSRF is fine.
- If the JWT is stored in a cookie, CSRF protection is needed again (Q48).
- With Spring Security, enable CORS in the security chain with `http.cors(...)`. Otherwise the CORS filter may run after security and the preflight gets a 401.

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("https://shop.example.com"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    config.setMaxAge(3600L);
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/api/**", config);
    return source;
}
```

**Follow-up questions:**

- **Can you use `allowedOrigins("*")` with credentials?**
  No. With `allowCredentials(true)` you must list specific origins (or use origin patterns).
- **Is CORS a server security feature?**
  No. It protects the user's browser. A non-browser client can call your API regardless. Real protection is authentication.
- **Why is a 403 on POST in a new Spring Security app common?**
  CSRF protection is on by default. A POST without a CSRF token is rejected. For a stateless token API, disable it deliberately.

---

### Q47. How does method security (@PreAuthorize) work? [Intermediate]

**Level: Intermediate**

**Short answer:** Method security checks permissions on service methods using annotations such as `@PreAuthorize("hasRole('ADMIN')")`. Enable it with `@EnableMethodSecurity` (Spring Security 6). It is proxy-based, like `@Transactional`.

**Explanation:**

- URL rules are coarse. Method rules express business permissions, such as "a user may read only THEIR orders".
- Annotations use SpEL (Spring Expression Language): `@PreAuthorize` (before), `@PostAuthorize` (check the returned object), `@PreFilter` / `@PostFilter` (filter collections).
- **Version note:** `@EnableMethodSecurity` replaces `@EnableGlobalMethodSecurity`. `@PreAuthorize` is on by default with it.
- Self-invocation and private methods are not protected (Q29).
- Failure throws `AccessDeniedException`, which becomes 403.
- Also enforce the rule in the query when you can (`findByIdAndCustomerId`). This avoids loading data you are not allowed to see.

```java
@Service
public class OrderService {

    @PreAuthorize("hasRole('ADMIN')")
    public void deleteOrder(Long id) {
    }

    @PreAuthorize("hasAuthority('SCOPE_orders.read')")
    public List<Order> listAll() {
        return List.of();
    }

    @PostAuthorize("returnObject.customerId == authentication.principal.id")
    public Order getOrder(Long id) {
        // loaded first, then checked: only the owner can read it
        return orderRepository.findById(id).orElseThrow();
    }
}
```

**Follow-up questions:**

- **`hasRole` vs `hasAuthority`?**
  `hasRole('ADMIN')` checks `ROLE_ADMIN`. `hasAuthority('ADMIN')` checks exactly `ADMIN`.
- **What is an IDOR bug?**
  Insecure Direct Object Reference: user A requests `/orders/42` that belongs to user B, and the app does not check ownership. A top API security risk. Method security or query filtering prevents it.
- **Where do you put the check, controller or service?**
  Service layer, so it also protects calls that do not come through HTTP (Kafka listeners, jobs).

---

### Q48. What are the JWT pitfalls: storage, expiry, refresh tokens, revocation? [Advanced]

**Level: Advanced**

**Short answer:** A JWT cannot be revoked before it expires, and anyone who steals it can use it. So keep access tokens short-lived (5 to 15 minutes), use rotating refresh tokens stored safely on the server side, and store tokens in HttpOnly cookies or memory, not `localStorage`.

**Explanation:**

**Where to store on the client:**

| Place | Risk |
|---|---|
| `localStorage` | Any XSS script can read it |
| HttpOnly, Secure, SameSite cookie | JavaScript cannot read it, but CSRF protection is needed |
| In memory | Lost on page refresh, then refresh via cookie |

**Access and refresh tokens:**

```text
Access token : short life (5-15 min), sent on every API call
Refresh token: long life (days), sent ONLY to /auth/refresh, stored hashed in the DB

Client --(access expired)--> POST /auth/refresh {refreshToken}
Server: find hash in DB, check not revoked or expired
        -> issue NEW access token + NEW refresh token, mark the old refresh token used
```

- **Rotation:** every refresh gives a new refresh token and invalidates the old one. If an old token is used again, that is theft: revoke the whole token family and force a re-login.
- **Revocation** (logout, password change, stolen token): since JWTs are stateless, keep a deny list of token IDs (`jti`) in Redis with a TTL equal to the token's remaining life. Or keep access tokens so short that revocation matters little.
- **Common mistakes:**
  - No signature check or accepting `alg: none`. Always pin the expected algorithm.
  - Not validating `exp`, `iss` and `aud` claims.
  - A weak or hard-coded HS256 secret in the repo. Use a long random secret from a secret store, or RS256 keys.
  - Sensitive data in the payload (it is readable).
  - Very long access token life.
  - Roles inside the token become stale until expiry.
- Key rotation: publish public keys with a `kid` header via a JWKS endpoint.

```java
public ResponseCookie refreshCookie(String refreshToken) {
    return ResponseCookie
        .from("refresh_token", refreshToken)
        .httpOnly(true)
        .secure(true)
        .sameSite("Strict")
        .path("/api/auth/refresh")
        .maxAge(Duration.ofDays(7))
        .build();
}
```

**Follow-up questions:**

- **Sessions or JWT?**
  Server sessions are easy to revoke but need shared storage (Redis) for many instances. JWTs scale without storage but are hard to revoke. Many apps use short JWTs plus a server-side refresh token.
- **How do you "log out" with JWT?**
  Delete the client token, revoke the refresh token, and deny-list the current `jti` if needed.
- **Is it safe to put the user id and roles in a JWT?**
  Yes, they are not secrets. Do not put passwords, personal data or anything you would not want read.

---

### Q49. What is an OAuth2 / OIDC resource server in Spring? [Advanced]

**Level: Advanced**

**Short answer:** In OAuth2, an authorization server (Keycloak, Auth0, Cognito) logs users in and issues tokens. Your Spring service is a **resource server**: it only validates the JWT and serves the API. OIDC (OpenID Connect) adds an ID token with user identity on top of OAuth2.

**Explanation:**

- **Roles:** resource owner (user), client (the app), authorization server (issues tokens), resource server (your API).
- Browser or mobile apps use the **authorization code flow with PKCE**. Service-to-service calls use the **client credentials flow**. Avoid the old password and implicit flows.
- The access token is for the API. The OIDC **ID token** is for the client, to know who logged in.
- Spring does not need your own JWT filter. Add `spring-boot-starter-oauth2-resource-server` and the issuer URL. Spring downloads the public keys (JWKS) and verifies signature, expiry and issuer.
- Scopes become authorities `SCOPE_xxx`. Map custom role claims with a `JwtAuthenticationConverter`.
- Do not write your own authorization server. Use Keycloak, Cognito or Spring Authorization Server.

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://auth.example.com/realms/shop
```

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/orders/**").hasAuthority("SCOPE_orders.read")
            .anyRequest().authenticated()
        )
        .oauth2ResourceServer(oauth2 -> oauth2
            .jwt(Customizer.withDefaults())
        );
    return http.build();
}
```

**Follow-up questions:**

- **Opaque token vs JWT?**
  An opaque token is a random string. The resource server calls the authorization server (introspection) to check it. Easy to revoke but adds a network call per check.
- **How does service A call service B securely?**
  With the client credentials flow: A gets a token for itself, then sends it to B (see Q50).

---

### Q50. Scenario: how do you secure a set of microservices? [Advanced]

**Level: Advanced**

**Short answer:** Authenticate users once at the edge (gateway) with a central identity provider, pass a JWT downstream, and let every service validate the token and check permissions itself. Add TLS, secrets management, input validation and rate limiting.

**Explanation:**

```text
Browser/Mobile --HTTPS--> API Gateway --(JWT)--> Order-Service --(client token)--> Inventory-Service
                              |                       |                                 |
                      validate token,         validate JWT again,             validate JWT again,
                      rate limit, CORS        check roles/ownership           check service scope
                              \                       |                                /
                               +------ Identity provider (Keycloak / Cognito): JWKS ---+
```

Layers:

1. **Identity:** one identity provider issues tokens. Services are resource servers (Q49).
2. **Gateway:** terminates TLS, rate limits, basic checks. Do NOT trust the gateway alone: every service must validate the token (zero trust). A service reachable inside the cluster can be called by anyone in the network.
3. **Authorization in each service:** roles and scopes plus ownership checks (Q47).
4. **Service-to-service:** client credentials tokens, or mutual TLS through a service mesh. Propagate the user token only when the downstream service needs the user identity.
5. **Secrets:** Kubernetes Secrets, AWS Secrets Manager or Vault. Not in git, not in images.
6. **Data:** input validation (Q17), no sensitive data in logs, encrypt data at rest and in transit, parameterised queries (JPA does this).
7. **Operations:** lock down Actuator (Q62), dependency scanning, audit logs, short token lifetimes, security tests in CI.
8. **Kafka:** authenticate and authorise producers and consumers (SASL, ACLs) and do not put secrets in events.

**Follow-up questions:**

- **Why validate the JWT again in every service?**
  Otherwise one bypassed gateway or a compromised pod exposes everything.
- **Where would you put rate limiting?**
  At the gateway for clients, and with Resilience4j `RateLimiter` for protecting expensive internal calls (Q53).
- **How do you rotate secrets without downtime?**
  Support two valid keys at once (key IDs), roll out the new one, then retire the old.

# Microservices with Spring

---

### Q51. What are service discovery, API gateway and config server? [Basic]

**Level: Basic**

**Short answer:** Service discovery lets services find each other by name instead of fixed IP. An API gateway is the single entry point for clients. A config server holds configuration in one place. On Kubernetes, you often use built-in features instead of the Spring Cloud versions.

**Explanation:**

| Need | Spring Cloud tool | Kubernetes alternative |
|---|---|---|
| Find services | Eureka (registry) + client-side load balancing (Spring Cloud LoadBalancer) | Service DNS name: `http://inventory-service` |
| Single entry, routing, rate limit, auth | Spring Cloud Gateway (reactive, built on WebFlux) | Ingress, or a gateway product (Kong, AWS API Gateway) |
| Central config | Spring Cloud Config Server (config in a Git repo) | ConfigMaps and Secrets |

- **Gateway:** routes `/api/orders/**` to Order-Service. Handles cross-cutting concerns: token validation, rate limiting, CORS, request logging, retries. Keep business logic out of it.
- **Config Server:** services fetch config at startup. `@RefreshScope` beans can reload on a refresh event without restart. Secrets still need Vault or a secrets manager, not plain Git.
- **Version note:** Spring Cloud versions must match the Boot version (Spring Cloud 2022.0.x and later for Boot 3). Check the compatibility table. Spring Cloud Sleuth, Hystrix and Zuul are not for Boot 3.
- Do not add Eureka and Config Server just because it is "microservices". If you run on Kubernetes, the platform already does discovery and config.

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
```

**Follow-up questions:**

- **Client-side vs server-side load balancing?**
  Client-side: the caller picks an instance from a list (Spring Cloud LoadBalancer). Server-side: a load balancer or Kubernetes Service picks it.
- **What happens if the Config Server is down at startup?**
  The service fails to start unless the import is `optional:` or a fallback exists. Another reason many teams prefer ConfigMaps.
- **Is the gateway a single point of failure?**
  Run several instances behind a load balancer, and keep it stateless.

---

### Q52. How does a circuit breaker work with Resilience4j? [Intermediate]

**Level: Intermediate**

**Short answer:** A circuit breaker stops calling a failing service. After too many failures it OPENS and fails fast for a while, instead of letting threads wait on timeouts. Then it lets a few trial calls through (HALF_OPEN) and closes again if they succeed.

**Explanation:**

```text
        failure rate >= threshold
CLOSED ----------------------------> OPEN  (calls fail at once, fallback runs)
  ^                                    |
  |  trial calls succeed               | after waitDurationInOpenState
  +------------- HALF_OPEN <-----------+
                    | trial calls fail -> back to OPEN
```

- Why it matters: if Inventory-Service is slow, Order-Service threads wait. All Tomcat threads end up blocked and Order-Service dies too. This is a **cascading failure**. The breaker cuts the chain.
- Use `resilience4j-spring-boot3` (Boot 3) with `spring-boot-starter-aop` and Actuator. For Boot 2 it was `resilience4j-spring-boot2`. Spring Cloud CircuitBreaker is a wrapper over it.
- Count-based window (last N calls) or time-based window. Set a **minimum number of calls** so 1 failure out of 1 call does not open it.
- A **fallback** gives a degraded answer: a cached value, a default, or a clear error. Do not hide failures: log and count them.
- Ignore business exceptions (like "not found" or validation errors). They are not service failures.
- Always combine with a timeout. A breaker cannot count a call that never ends.

```yaml
resilience4j:
  circuitbreaker:
    instances:
      inventory:
        sliding-window-type: COUNT_BASED
        sliding-window-size: 20
        minimum-number-of-calls: 10
        failure-rate-threshold: 50
        slow-call-duration-threshold: 2s
        slow-call-rate-threshold: 80
        wait-duration-in-open-state: 30s
        permitted-number-of-calls-in-half-open-state: 3
        ignore-exceptions:
          - com.example.order.StockNotFoundException
```

```java
@Service
public class StockService {

    @CircuitBreaker(name = "inventory", fallbackMethod = "stockFallback")
    public StockResponse getStock(String sku) {
        return inventoryRestClient
            .get()
            .uri("/stock/{sku}", sku)
            .retrieve()
            .body(StockResponse.class);
    }

    private StockResponse stockFallback(String sku, Throwable ex) {
        return StockResponse.unknown(sku);
        // degraded answer: stock is unknown
    }
}
```

**Follow-up questions:**

- **What does the circuit breaker NOT protect against?**
  Slow calls without a timeout, and overload from too many concurrent calls (use bulkhead).
- **What happens when the circuit is open?**
  The call throws `CallNotPermittedException` at once, and the fallback runs.
- **How do you watch it?**
  Actuator `circuitbreakers` endpoint and Micrometer metrics `resilience4j_circuitbreaker_state` (Q64).
- **Is the annotation self-invocation safe?**
  No. Resilience4j annotations are proxy-based (Q29).

---

### Q53. How do retry, bulkhead, rate limiter and time limiter work, and in what order do they apply? [Intermediate]

**Level: Intermediate**

**Short answer:** Retry repeats a failed call. Bulkhead limits concurrent calls so one dependency cannot use all threads. Rate limiter limits calls per period. Time limiter sets a maximum duration. Combine them carefully and retry only idempotent operations.

**Explanation:**

- **Retry:** set `max-attempts`, a wait with **exponential backoff** and **jitter** (random delay), and the exceptions to retry. Retrying on every error multiplies load on a struggling service (**retry storm**). Never retry non-idempotent POSTs without an idempotency key (Q23). Retry only transient errors (timeouts, 503), not 400 or 404.
- **Bulkhead:** a semaphore bulkhead limits parallel calls (for example max 10 to Inventory). A thread-pool bulkhead gives that dependency its own thread pool. Extra calls fail at once (`BulkheadFullException`).
- **Rate limiter:** `limit-for-period` calls per `limit-refresh-period`. Protects a downstream or a costly operation.
- **Time limiter:** limits how long a `CompletableFuture` call can run. For plain blocking calls, a client read timeout is the real protection.
- **Default aspect order** (outermost first), per the Resilience4j docs:

```text
Retry ( CircuitBreaker ( RateLimiter ( TimeLimiter ( Bulkhead ( your call )))))
```

So a retry wraps the circuit breaker: each attempt is counted by the breaker, and if the circuit is open, the retry sees `CallNotPermittedException` (do not retry that). Check the order values for your version.

```yaml
resilience4j:
  retry:
    instances:
      inventory:
        max-attempts: 3
        wait-duration: 200ms
        enable-exponential-backoff: true
        exponential-backoff-multiplier: 2
        enable-randomized-wait: true
        retry-exceptions:
          - java.io.IOException
          - org.springframework.web.client.ResourceAccessException
        ignore-exceptions:
          - com.example.order.StockNotFoundException
  bulkhead:
    instances:
      inventory:
        max-concurrent-calls: 10
        max-wait-duration: 0
  ratelimiter:
    instances:
      inventory:
        limit-for-period: 50
        limit-refresh-period: 1s
        timeout-duration: 0
```

```java
@Retry(name = "inventory")
@Bulkhead(name = "inventory")
@CircuitBreaker(name = "inventory", fallbackMethod = "stockFallback")
public StockResponse getStock(String sku) {
    return inventoryClient.getStock(sku);
}
```

**Follow-up questions:**

- **Why add jitter to backoff?**
  Without it, many clients retry at the same moments and hit the server together.
- **Total latency with 3 retries and a 5 s timeout?**
  Up to about 15 s plus waits. Make sure it is shorter than the caller's own timeout, or the caller gives up while you still retry.
- **Bulkhead vs rate limiter?**
  Bulkhead limits how many calls run AT THE SAME TIME. Rate limiter limits how many calls START per time period.

---

### Q54. How does distributed tracing work in Spring Boot 3? [Intermediate]

**Level: Intermediate**

**Short answer:** A trace is the path of one request across services. Each step is a span. A trace ID is passed in HTTP and Kafka headers, and added to logs, so you can find all logs and timings for one request. Boot 3 uses Micrometer Tracing, which replaced Spring Cloud Sleuth.

**Explanation:**

```text
traceId = abc123
[ API Gateway span ]
   [ Order-Service span: POST /orders ]
        [ DB span ]
        [ Inventory-Service span: GET /stock ]
        [ Kafka send span: order-events ]
              [ Inventory-Service consumer span ]
```

- **Version note:** Sleuth does NOT work with Boot 3. Use `micrometer-tracing-bridge-otel` (or the Brave bridge) plus an exporter (OTLP to Jaeger, Tempo or Zipkin), and `spring-boot-starter-actuator`.
- The trace ID is placed in the **MDC** (logging context). Include it in the log pattern so a user report with a trace ID leads to all logs. Boot 3.2+ has `logging.pattern.correlation` for this.
- Propagation uses the W3C `traceparent` header. It works automatically when you create clients from Boot's auto-configured builders (`RestClient.Builder`, `WebClient.Builder`, `RestTemplateBuilder`). A `new RestTemplate()` created by hand does not propagate.
- For Kafka, enable observation on the template and listener container, so headers are written and read (properties like `spring.kafka.template.observation-enabled` and `spring.kafka.listener.observation-enabled`; check your Boot version).
- Use sampling in production (for example 10 percent) to control cost: `management.tracing.sampling.probability`.
- Thread hops (`@Async`, custom executors) lose the context unless it is propagated by a `TaskDecorator` or context-propagation library.

```yaml
management:
  tracing:
    sampling:
      probability: 0.1
  otlp:
    tracing:
      endpoint: http://otel-collector:4318/v1/traces
logging:
  pattern:
    level: "%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]"
```

**Follow-up questions:**

- **Tracing vs metrics vs logs?**
  Metrics tell you that something is slow. Traces show where. Logs show why.
- **Why is my trace broken between services?**
  A client was created without the auto-configured builder, or a message broker or async hop does not carry the headers.
- **What is a correlation ID?**
  A simple request ID passed in headers and logs. Tracing does this and more.

---

### Q55. How do you configure a Spring Kafka consumer, handle errors and use a dead-letter topic? [Intermediate]

**Level: Intermediate**

**Short answer:** Use `@KafkaListener`, turn off auto-commit and let the container commit offsets after your method finishes. Handle failures with a `DefaultErrorHandler`: retry with backoff, then publish the failed record to a dead-letter topic (DLT) with `DeadLetterPublishingRecoverer`.

**Explanation:**

(See the Kafka notes for the Kafka side. This is the Spring side.)

- The listener container polls, calls your method, and commits offsets according to `ack-mode`. Default `BATCH` commits after a poll's records are processed. `RECORD` commits after each record. `MANUAL` lets you call `ack.acknowledge()`.
- `spring.kafka.consumer.enable-auto-commit` is `false` in Boot's defaults for the listener container. Do not enable it.
- `concurrency` creates that many consumer threads in the same group. Useful up to the number of partitions.
- Keep processing time per poll under `max.poll.interval.ms`. A slow listener causes a rebalance.
- **Error handling:**
  - Exceptions thrown from the listener go to the `DefaultErrorHandler`. Default: 9 retries immediately (10 attempts), then log and skip the record. That is NOT what you want for important data.
  - Configure a `BackOff` and a recoverer (DLT).
  - Mark exceptions that retrying cannot fix as **not retryable** (deserialisation, validation): they go straight to the DLT.
- **Poison pill** (a record that always fails to deserialise) blocks the partition. Wrap the deserialiser in `ErrorHandlingDeserializer`.
- Retries inside the container are **blocking**: the partition waits. For long delays use non-blocking retry topics with `@RetryableTopic`.

```yaml
spring:
  kafka:
    bootstrap-servers: kafka:9092
    consumer:
      group-id: inventory-service
      auto-offset-reset: earliest
      enable-auto-commit: false
      max-poll-records: 100
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.ErrorHandlingDeserializer
      properties:
        spring.deserializer.value.delegate.class: org.springframework.kafka.support.serializer.JsonDeserializer
        spring.json.trusted.packages: com.example.events
    listener:
      concurrency: 3
      ack-mode: record
```

```java
@Configuration
public class KafkaConsumerConfig {

    @Bean
    public DefaultErrorHandler errorHandler(KafkaTemplate<Object, Object> template) {
        DeadLetterPublishingRecoverer recoverer = new DeadLetterPublishingRecoverer(template);
        // failed record goes to "order-events.DLT" (same partition number by default)
        ExponentialBackOffWithMaxRetries backOff = new ExponentialBackOffWithMaxRetries(4);
        backOff.setInitialInterval(500);
        backOff.setMultiplier(2.0);
        DefaultErrorHandler handler = new DefaultErrorHandler(recoverer, backOff);
        handler.addNotRetryableExceptions(IllegalArgumentException.class);
        return handler;
    }
}

@Component
public class OrderEventListener {

    @KafkaListener(topics = "order-events", groupId = "inventory-service")
    public void onOrderCreated(OrderCreatedEvent event) {
        // reserve stock; throw on failure so the error handler can retry
    }
}
```

**Follow-up questions:**

- **How many DLT partitions are needed?**
  The default recoverer publishes to the SAME partition number, so the DLT needs at least as many partitions as the original topic, or you give it a custom destination resolver.
- **Blocking retry vs `@RetryableTopic`?**
  Blocking retry stops the partition and keeps order. `@RetryableTopic` moves the record to retry topics so other records continue, but ordering is lost for that key.
- **What do you do with messages in the DLT?**
  Alert on them, fix the cause, then replay after the fix. Include the original exception in headers (the recoverer adds them).
- **Why does the listener get the same message again?**
  Retry after an exception, a rebalance before commit, or a duplicate from the producer. Consumers must be idempotent (Q56).

---

### Q56. How do you make a Kafka consumer idempotent, and what about Kafka transactions? [Advanced]

**Level: Advanced**

**Short answer:** Kafka gives at-least-once delivery by default, so duplicates happen. Make the consumer idempotent by recording processed event IDs in the same database transaction as the business change, behind a unique constraint. Kafka transactions give exactly-once only for Kafka-to-Kafka work, NOT for database plus Kafka.

**Explanation:**

**Idempotent consumer pattern:**

```java
@Transactional
@KafkaListener(topics = "order-events", groupId = "inventory-service")
public void onOrderCreated(OrderCreatedEvent event) {
    try {
        processedEventRepository.saveAndFlush(new ProcessedEvent(event.eventId()));
        // unique constraint on event_id
    } catch (DataIntegrityViolationException duplicate) {
        return;
        // already processed: skip
    }
    inventoryService.reserve(event.sku(), event.quantity());
    // stock update and processed-event row commit together
}
```

- Both the processed-event row and the stock change are in ONE DB transaction. Either both happen or none.
- Trap: after a unique-constraint failure inside the repository call, the transaction is usually marked rollback-only (same note as Q23). Catching the exception and returning then ends in `UnexpectedRollbackException` at commit. Safer: check `existsByEventId` first and keep the unique constraint as the final guard, or insert the id in a `REQUIRES_NEW` transaction.
- Alternative: make the operation naturally idempotent (for example "set status to PAID" instead of "add 10"), or use the business key with a unique constraint.
- Use a stable `eventId` created by the producer, not Kafka's offset.
- Clean up old processed-event rows after the retention period.

**Producer side in Spring:**

- Idempotent producer (`enable.idempotence=true`, default in Kafka clients 3.0+) stops duplicates caused by producer retries.
- `KafkaTemplate.send` is asynchronous and returns a future. Handle the result, or you will not know about failures.

**Kafka transactions in Spring (consume-process-produce):**

- Set `spring.kafka.producer.transaction-id-prefix=order-tx-`. Boot then creates a `KafkaTransactionManager`.
- The listener container starts a Kafka transaction per record or batch. The consumed offsets and the produced messages commit atomically.
- Downstream consumers must use `isolation.level=read_committed`.
- The guarantee is only inside Kafka. If your listener also writes to MySQL, the DB commit and Kafka commit are two separate commits. A crash between them gives duplicates or lost writes. Handle it with the idempotent consumer pattern, or the outbox (Q57).
- Transactions cost throughput and need care with `transaction-id-prefix` per instance. Many teams skip them and use at-least-once plus idempotency.

```java
@KafkaListener(topics = "order-events", groupId = "inventory-service")
public void process(OrderCreatedEvent event) {
    InventoryReservedEvent result = inventoryService.reserve(event);
    kafkaTemplate.send("inventory-events", event.orderId().toString(), result);
    // with a transaction-id-prefix set, this send and the offset commit are atomic
}
```

**Follow-up questions:**

- **Is "exactly-once" real?**
  Only within Kafka (idempotent producer + transactions + read_committed). End-to-end with a database it is "effectively once": at-least-once delivery plus idempotent processing.
- **Where do you store processed IDs, DB or Redis?**
  DB in the same transaction is the safest. Redis is faster, but not atomic with the DB.
- **What about ordering during reprocessing?**
  Use the order id as the key, so events for one order stay in one partition and in order.

---

### Q57. How do you implement the outbox pattern and a saga in Spring? [Advanced]

**Level: Advanced**

**Short answer:** The outbox pattern solves the "write to DB and send to Kafka" problem: save the business data and an outbox row in one DB transaction, then a separate process publishes the outbox rows to Kafka. A saga is a series of local transactions with compensating actions, used instead of a distributed transaction.

**Explanation:**

**Problem (dual write):** if you save the order and then call Kafka, a crash between the two loses the event. If you send first and the DB fails, you sent an event for an order that does not exist.

```text
Order-Service (one DB transaction)
   INSERT orders (id=42, status=PENDING)
   INSERT outbox_event (id=uuid, aggregate_id=42, type=OrderCreated, payload=json, sent=false)
COMMIT
        |
        v   (relay)
   Poller or Debezium CDC reads outbox rows -> Kafka topic order-events (key=42) -> marks sent
```

- **Relay options:** a polling `@Scheduled` job (simple, small delay, protect with a lock such as `FOR UPDATE SKIP LOCKED`), or Debezium change data capture reading the DB log (low latency, more infrastructure).
- The relay is at-least-once, so consumers must be idempotent (Q56). Put a unique `eventId` in each event.
- Delete or archive sent rows regularly.
- Publishing right after commit with `@TransactionalEventListener(phase = AFTER_COMMIT)` is simpler, but it can still lose the event if the app crashes just after commit. It is not a full outbox.

```java
@Transactional
public Order createOrder(CreateOrderRequest request) {
    Order order = orderRepository.save(Order.pending(request));
    OutboxEvent event = new OutboxEvent(
        UUID.randomUUID(),
        "Order",
        order.getId().toString(),
        "OrderCreated",
        toJson(OrderCreatedEvent.from(order))
    );
    outboxRepository.save(event);
    return order;
}

@Scheduled(fixedDelay = 1000)
@Transactional
public void publishPending() {
    List<OutboxEvent> batch = outboxRepository.lockNextBatch(100);
    // select ... where sent = false order by id limit 100 for update skip locked
    for (OutboxEvent event : batch) {
        kafkaTemplate.send("order-events", event.getAggregateId(), event.getPayload());
        event.markSent();
    }
}
```

(Production code should wait for the send result before marking rows as sent.)

**Saga (choreography) for placing an order:**

```text
Order-Service: create order PENDING -> event OrderCreated
Inventory-Service: reserve stock -> OK: InventoryReserved   | FAIL: InventoryRejected
Payment-Service:   on InventoryReserved -> charge -> PaymentCompleted | PaymentFailed
Order-Service:     PaymentCompleted -> CONFIRMED
                   InventoryRejected or PaymentFailed -> CANCELLED (and ask Inventory to release stock)
```

- **Choreography:** each service reacts to events. Simple for few steps, hard to follow with many.
- **Orchestration:** a central orchestrator (a class or a workflow engine such as Temporal) tells each service what to do and handles compensation. Easier to see and test the flow.
- Each step needs a **compensation** (undo): release stock, refund payment. Compensation must also be idempotent.
- The order status shows progress (PENDING, CONFIRMED, CANCELLED). The user sees "eventual consistency".

**Follow-up questions:**

- **Why not 2PC (two-phase commit)?**
  It locks resources across services, reduces availability, and Kafka does not take part in XA with your DB.
- **Outbox with Debezium or polling?**
  Start with polling for simplicity. Move to CDC when volume or latency needs it.
- **How do you handle a saga step that times out?**
  Use a timeout event or a scheduled check that moves old PENDING orders to CANCELLED and compensates.

---

### Q58. How do you keep an API backward compatible and version it? [Advanced]

**Level: Advanced**

**Short answer:** Make only additive changes (new optional fields, new endpoints) so old clients keep working. When a breaking change is unavoidable, add a new version (such as `/v2/orders`) and keep the old one until clients migrate.

**Explanation:**

- **Safe changes:** add an optional response field, add an optional request field with a default, add an endpoint, add a new enum value ONLY if clients tolerate unknown values.
- **Breaking changes:** remove or rename a field, change a type, make an optional field required, change the meaning of a field, change status codes.
- **Tolerant reader:** clients ignore fields they do not know. Boot's Jackson config has `FAIL_ON_UNKNOWN_PROPERTIES` turned OFF by default. Keep it off in clients and in Kafka consumers.
- **Versioning styles:**

| Style | Example | Note |
|---|---|---|
| URI path | `/api/v1/orders` | Simplest, most common, visible in logs |
| Header | `X-API-Version: 2` or a media type | Clean URLs, harder to test in a browser |
| Query param | `/orders?version=2` | Rarely used |

- Spring Boot 3.x has no built-in API version support. You map versions with separate controllers or `@RequestMapping` paths. **Version note:** Spring Framework 7 adds first-class API versioning, so check if you are on that.
- **Expand and contract** for data and events: add the new field, run both, migrate consumers, remove the old field. Same for database schema (Q31).
- For Kafka events, use a schema registry (Avro or JSON Schema) with compatibility rules, or add a `version` field in the event.
- **Contract tests** (Spring Cloud Contract or Pact) check that a producer change does not break consumers, before deploy.
- Deprecate with a notice: response headers (`Deprecation`, `Sunset`), docs and metrics showing who still uses v1.

```java
@RestController
@RequestMapping("/api/v1/orders")
public class OrderControllerV1 {
}

@RestController
@RequestMapping("/api/v2/orders")
public class OrderControllerV2 {
    // new response shape. The service layer is shared.
}
```

**Follow-up questions:**

- **Do mobile apps change the strategy?**
  Yes. You cannot force users to update, so keep old versions for a long time and track usage per version.
- **Is renaming a field backward compatible?**
  No. Add the new name, keep writing both, then remove the old one in a later version.
- **How do you detect breaking changes automatically?**
  OpenAPI diff tools in CI, and consumer-driven contract tests.

---

### Q59. Scenario: design an Order Service that must not lose or duplicate orders [Advanced]

**Level: Advanced**

**Short answer:** Accept orders with an idempotency key, save order and outbox event in one DB transaction, publish to Kafka from the outbox, process downstream steps with idempotent consumers, and protect all calls with timeouts and circuit breakers.

**Explanation:**

Requirement: `POST /orders` for Order-Service. Stock lives in Inventory-Service, payment in Payment-Service. Traffic spikes during sales.

```text
Client --POST /orders + Idempotency-Key--> API Gateway --> Order-Service
   Order-Service: validate -> check idempotency key -> TX{ save order PENDING + outbox row } -> 202/201
   Outbox relay -> Kafka [order-events] -> Inventory-Service (idempotent consumer) -> [inventory-events]
   -> Payment-Service -> [payment-events] -> Order-Service updates status CONFIRMED / CANCELLED
   DLT topics + alerts for poison messages
```

Design decisions, with the reason:

1. **API:** DTO validation (Q17), `Idempotency-Key` (Q23), return `201` with a `Location`, or `202 Accepted` if the work is asynchronous. Client polls `GET /orders/{id}` or gets a notification.
2. **Consistency:** local DB transaction plus outbox (Q57). No distributed transaction.
3. **Stock:** the reservation is atomic in Inventory-Service (atomic `UPDATE ... WHERE available >= ?` or locking, Q32). Prevents overselling.
4. **Messaging:** key = orderId for ordering per order. `acks=all`, idempotent producer, `min.insync.replicas=2`. Consumers are idempotent (Q56). DLT for poison messages (Q55).
5. **Failure handling:** saga compensations (release stock, refund). Order status machine with timeouts.
6. **Resilience:** timeouts on every call, circuit breaker and bulkhead around sync calls (Q52, Q53). If a sync stock check is needed, define a fallback.
7. **Scaling:** stateless Order-Service pods, Hikari pool sized for the DB (Q34), partitions at least as many as consumers.
8. **Observability:** trace ID across HTTP and Kafka (Q54), metrics for order rate, error rate, consumer lag, outbox backlog, saga stuck count (Q64).
9. **Security:** JWT validation, ownership checks (Q47, Q50).
10. **Data:** Flyway migrations (Q31), each service owns its DB (no shared tables).

**Follow-up questions:**

- **Sync call to Inventory or events?**
  Events decouple and survive downtime, but the user gets an eventual answer. A sync check gives an instant answer but couples availability. Many designs do a quick sync "is it available" check and then the real reservation by events.
- **What if Kafka is down?**
  The outbox keeps accepting orders. The backlog drains when Kafka returns. Monitor outbox size.
- **How do you test it?**
  Unit tests for the saga logic, Testcontainers (MySQL + Kafka) integration tests, contract tests, and chaos tests such as killing a pod mid-flow.

---

### Q60. Scenario: how do you migrate one piece of a monolith to a microservice? [Advanced]

**Level: Advanced**

**Short answer:** Use the strangler fig approach: put a router in front of the monolith, build the new service for one bounded capability, move traffic gradually, and migrate its data in steps. Do not rewrite everything at once.

**Explanation:**

Example: extract "Inventory" from a monolith into Inventory-Service.

1. **Pick the seam:** a part with clear boundaries, its own data and a stable interface. Inventory has a clear owner and different scaling needs.
2. **Make the boundary inside the monolith first:** put inventory behind an interface, remove cross-table joins and shared transactions with other modules.
3. **Add a gateway/router:** all traffic goes through it. Initially everything routes to the monolith.
4. **Build the new service** with its own database. Start with read paths and low-risk flows.
5. **Sync data:** dual write is risky. Prefer change data capture (Debezium) or an outbox from the monolith to feed the new service. Run both and compare results (shadow traffic).
6. **Move traffic gradually:** route 1 percent, then 10 percent, then all, using gateway rules or feature flags. Keep a quick rollback.
7. **Cut over data ownership:** the new service becomes the source of truth. The monolith calls it through an API or events. Remove the old code and tables.
8. **Replace joins and transactions:** use API calls or events, and sagas (Q57) where one transaction used to span modules.

```text
Phase 1:  Client -> Gateway -> Monolith (all)
Phase 2:  Client -> Gateway -> /inventory/** -> Inventory-Service (new)   others -> Monolith
Phase 3:  Monolith calls Inventory-Service by API/events; old inventory tables removed
```

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: inventory-new
          uri: lb://inventory-service
          predicates:
            - Path=/api/inventory/**
            - Weight=inventory, 10
        - id: inventory-old
          uri: http://monolith:8080
          predicates:
            - Path=/api/inventory/**
            - Weight=inventory, 90
```

**Follow-up questions:**

- **What are the risks?**
  Data inconsistency during sync, a "distributed monolith" (services too chatty and coupled), and more operations work (deploy, monitoring).
- **When should you NOT split?**
  When the team is small, the boundaries are unclear, or the monolith is fine. A modular monolith is a good middle step.
- **How do you shadow-test?**
  Send a copy of the traffic to the new service, compare the answers, and ignore its response for users.

---

# Testing and Production

---

### Q61. Unit test vs slice test vs @SpringBootTest: what do you use when? [Basic]

**Level: Basic**

**Short answer:** Unit tests run without Spring and are the fastest. Slice tests (`@WebMvcTest`, `@DataJpaTest`) load only one layer of the context. `@SpringBootTest` loads the full application and is the slowest. Use the smallest test that proves the behaviour.

**Explanation:**

| Test type | Loads | Use for |
|---|---|---|
| Plain JUnit + Mockito | Nothing from Spring | Business logic in services |
| `@WebMvcTest(OrderController.class)` | MVC layer only (controllers, advice, filters). Services are mocked. | Status codes, JSON, validation, security rules |
| `@DataJpaTest` | JPA repositories and an embedded or container DB | Queries, mappings, `@Query` |
| `@JsonTest`, `@RestClientTest` | Only JSON or a client | Serialisation, client behaviour |
| `@SpringBootTest` | Full context (optionally with a real port) | End-to-end flows, wiring problems |

- Context caching: Spring reuses a context between tests with the same configuration. Every `@MockBean` or `@MockitoBean` change creates a new context and slows the build.
- A test pyramid: many unit tests, some slice tests, few full integration tests.
- `@DataJpaTest` replaces your DB with an embedded H2 by default. H2 behaves differently from MySQL, so for real queries use Testcontainers with `@AutoConfigureTestDatabase(replace = NONE)` (Q63).

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @InjectMocks
    private OrderService orderService;

    @Test
    void shouldRejectEmptyOrder() {
        assertThrows(
            IllegalArgumentException.class,
            () -> orderService.create(new CreateOrderRequest(1L, List.of()))
        );
    }
}
```

**Follow-up questions:**

- **Why is `@SpringBootTest` slow?**
  It starts the full context, possibly a server and DB connections. Use it sparingly.
- **`@SpringBootTest(webEnvironment = RANDOM_PORT)` vs MOCK?**
  MOCK (default) uses a mock servlet environment. RANDOM_PORT starts a real server so you can use `TestRestTemplate` or `WebTestClient`.
- **What does `@WebMvcTest` NOT load?**
  Services, repositories and other `@Component` beans. You provide mocks.

---

### Q62. What is Spring Boot Actuator, and how do health probes work in Kubernetes? [Basic]

**Level: Basic**

**Short answer:** Actuator adds production endpoints: health, metrics, info, loggers, thread dump and more. For Kubernetes, use separate liveness and readiness probes: liveness says "restart me if I am stuck", readiness says "send me traffic only when I am ready".

**Explanation:**

- Add `spring-boot-starter-actuator`. Only `health` is exposed over HTTP by default. Expose others deliberately.
- Useful endpoints: `/actuator/health`, `/actuator/metrics`, `/actuator/prometheus` (with the Micrometer Prometheus registry), `/actuator/loggers` (change log level at runtime), `/actuator/threaddump`, `/actuator/heapdump`, `/actuator/conditions`, `/actuator/env`.
- **Secure Actuator.** `env`, `heapdump` and `beans` leak sensitive data. Expose few endpoints, require auth, or run Actuator on a separate management port not exposed outside the cluster.
- **Probes:** `/actuator/health/liveness` and `/actuator/health/readiness`. Boot enables them automatically on Kubernetes (or with `management.endpoint.health.probes.enabled=true`).
- Do NOT put external dependencies (database, Kafka) into the LIVENESS check. If the DB is down, restarting every pod does not help and makes it worse. Dependencies can be part of READINESS, with care.
- During graceful shutdown readiness goes to "refusing traffic" first (Q65).

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      probes:
        enabled: true
      show-details: when-authorized
  server:
    port: 9090
```

```yaml
# Kubernetes container spec (excerpt)
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 9090
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 9090
  periodSeconds: 5
startupProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 9090
  failureThreshold: 30
  periodSeconds: 5
```

**Follow-up questions:**

- **Liveness vs readiness vs startup probe?**
  Liveness: restart if failing. Readiness: remove from load balancing if failing. Startup: give a slow-starting app time before liveness begins.
- **How do you add a custom health check?**
  Implement `HealthIndicator` and return `Health.up()` or `Health.down()`.
- **Why can a bad liveness check cause outages?**
  A slow dependency makes all pods fail liveness, and Kubernetes restarts them all at once.

---

### Q63. How do you test controllers and repositories with MockMvc, @MockitoBean and Testcontainers? [Intermediate]

**Level: Intermediate**

**Short answer:** Use `MockMvc` with `@WebMvcTest` for controllers, mock the service, and check status, JSON and validation. Use Testcontainers to run a real MySQL or Kafka in Docker for repository and integration tests. `@MockBean` was replaced by `@MockitoBean` (Spring 6.2 / Boot 3.4).

**Explanation:**

- `@MockBean` (Boot) is deprecated since Boot 3.4 in favour of `@MockitoBean` (Spring Framework 6.2). Use whichever your version supports.
- With Spring Security on the classpath, `@WebMvcTest` applies the security filters. Use `@WithMockUser` or `jwt()` request post-processors, and test both allowed and denied cases.
- **Testcontainers** starts real dependencies in Docker for tests. `@ServiceConnection` (Boot 3.1+) wires the container's URL into Spring automatically, replacing manual `@DynamicPropertySource`. Needs Docker in the CI agent (Jenkins).
- Real MySQL in tests catches problems H2 hides: SQL dialect, locking, JSON columns, collation, Flyway scripts.
- Make the container `static` so it is shared by the test class, and reuse the context.

```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockitoBean
    private OrderService orderService;

    @Test
    @WithMockUser
    void shouldReturn400WhenBodyInvalid() throws Exception {
        mockMvc
            .perform(
                post("/api/orders")
                    .contentType(MediaType.APPLICATION_JSON)
                    .content("{\"customerId\": null, \"items\": []}")
                    .with(csrf())
            )
            .andExpect(status().isBadRequest());
    }
}
```

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class OrderRepositoryTest {

    @Container
    @ServiceConnection
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0");

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void shouldFindByStatus() {
        orderRepository.save(Order.newOrder(1L));
        assertEquals(1, orderRepository.findByStatus(OrderStatus.NEW).size());
    }
}
```

**Follow-up questions:**

- **How do you test Kafka code?**
  Testcontainers `KafkaContainer`, or `@EmbeddedKafka` from spring-kafka-test. Prefer the container, as it matches production.
- **How do you test security rules?**
  `@WebMvcTest` plus `@WithMockUser(roles = "ADMIN")` and `.with(jwt())` from `SecurityMockMvcRequestPostProcessors`.
- **Why do tests keep restarting the context?**
  Different `@MockitoBean` sets or properties per class produce different context cache keys.

---

### Q64. How do you set up metrics with Micrometer, Prometheus and Grafana? [Intermediate]

**Level: Intermediate**

**Short answer:** Micrometer is the metrics facade in Boot. Add the Prometheus registry and Actuator, and the app exposes `/actuator/prometheus`. Prometheus scrapes it on a schedule and stores the data. Grafana draws dashboards and sends alerts from Prometheus.

**Explanation:**

```text
Spring Boot app --(/actuator/prometheus, pull every 15s)--> Prometheus --(PromQL)--> Grafana dashboards + alert rules
```

- Boot auto-collects: `http.server.requests` (rate, errors, latency), JVM memory and GC, threads, Hikari pool, Tomcat, Kafka client metrics, Resilience4j.
- Prometheus names use underscores: `http_server_requests_seconds_count`.
- Custom metrics: `Counter` (events counted, only goes up), `Gauge` (current value), `Timer` (duration with percentiles), `DistributionSummary`.
- Enable histograms to compute p95/p99 in Prometheus across instances: `management.metrics.distribution.percentiles-histogram.http.server.requests=true`. Do not use client-side percentiles for aggregation across instances, as they cannot be combined.
- **Cardinality:** never use unbounded values (user id, order id, raw URL) as tags. It creates millions of series and kills Prometheus. Use templates like `/orders/{id}` (Boot does this for you).
- Add common tags: `management.metrics.tags.application=order-service`.
- Alert on symptoms: error rate, p99 latency, consumer lag, pool pending, readiness, not only CPU.

```java
@Service
public class OrderMetrics {

    private final Counter ordersCreated;
    private final Timer paymentTimer;

    public OrderMetrics(MeterRegistry registry) {
        this.ordersCreated = Counter
            .builder("orders.created")
            .tag("channel", "web")
            .register(registry);
        this.paymentTimer = Timer
            .builder("orders.payment.duration")
            .publishPercentileHistogram()
            .register(registry);
    }

    public void orderCreated() {
        ordersCreated.increment();
    }

    public void recordPayment(Runnable payment) {
        paymentTimer.record(payment);
    }
}
```

```text
# PromQL examples
# Error rate (5xx) over 5 minutes
sum(rate(http_server_requests_seconds_count{status=~"5.."}[5m]))
  / sum(rate(http_server_requests_seconds_count[5m]))

# p99 latency
histogram_quantile(0.99, sum by (le) (rate(http_server_requests_seconds_bucket[5m])))

# Connections waiting for the pool
hikaricp_connections_pending
```

**Follow-up questions:**

- **Pull vs push?**
  Prometheus pulls (scrapes). Short-lived jobs use a Pushgateway. In Kubernetes, discovery finds pods through annotations or a ServiceMonitor.
- **RED vs USE method?**
  RED for services: Rate, Errors, Duration. USE for resources: Utilisation, Saturation, Errors.
- **Why is `@Timed` not working on my method?**
  It needs the aspect (`TimedAspect` bean) and the call must go through the proxy.

---

### Q65. How do you do graceful shutdown and package Spring Boot in Docker, including JVM memory in containers? [Intermediate]

**Level: Intermediate**

**Short answer:** Enable graceful shutdown so in-flight requests finish before the app stops. Build a small layered image that runs as non-root. In containers, let the JVM size heap as a percentage of the container limit (`-XX:MaxRAMPercentage`), and leave room for non-heap memory.

**Explanation:**

**Graceful shutdown:**

- On SIGTERM (what Kubernetes sends), with `server.shutdown=graceful` the server stops accepting new requests and waits for active ones, up to `spring.lifecycle.timeout-per-shutdown-phase`.
- Kafka listeners stop and commit offsets. Close pools after in-flight work finishes.
- Kubernetes: pod removal and traffic routing are not instant, so some requests still arrive after SIGTERM. Add a short `preStop` sleep, and set `terminationGracePeriodSeconds` larger than the shutdown timeout. Graceful shutdown is the default since Boot 3.4 (older versions default to `immediate`), so set it explicitly if you support older versions.

```yaml
server:
  shutdown: graceful
spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s
```

**Docker image:**

- Use a JRE base image, run as non-root, and use layered JARs so dependency layers are cached between builds. Alternatively `mvn spring-boot:build-image` (Cloud Native Buildpacks) builds an image without a Dockerfile.
- **Version note:** the layer extraction command is `java -Djarmode=tools -jar app.jar extract --layers` in Boot 3.3+ (it was `-Djarmode=layertools` before).

```dockerfile
FROM eclipse-temurin:21-jre AS runtime
WORKDIR /app
RUN useradd --system --uid 1001 appuser
COPY target/order-service.jar app.jar
USER appuser
EXPOSE 8080
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

**JVM memory in containers:**

- Modern JVMs (Java 10+, and 8u191+) read the container memory limit (cgroups). Without flags, default max heap is about 25 percent of it, which wastes memory.
- Container memory = heap + metaspace + thread stacks + direct buffers + code cache + GC overhead. If heap is 100 percent of the limit, the Linux OOM killer kills the container (exit code 137, `OOMKilled`).
- A common start: `-XX:MaxRAMPercentage=70` to `75`, then measure. Set CPU requests and limits too. The JVM sizes thread pools and GC threads from the CPU count it sees.
- Use `-XX:+HeapDumpOnOutOfMemoryError` and write dumps to a mounted volume.

```text
Container limit 1 GiB
  Heap (75%) ........ ~768 MiB
  Metaspace, threads, direct buffers, code cache, GC ... ~256 MiB (must fit in the rest)
```

**Follow-up questions:**

- **Pod killed with exit code 137?**
  OOMKilled: total memory passed the limit. This is different from `java.lang.OutOfMemoryError` (heap full inside the JVM). Look at non-heap memory and the heap percentage.
- **Why does the app get 502s during a deploy?**
  Traffic still reached a pod that was stopping. Use readiness, graceful shutdown, `preStop` delay, and rolling update settings (`maxUnavailable: 0`).
- **Should you use `-Xmx` instead?**
  `-Xmx` is fixed and does not scale when the limit changes. Percentage flags are easier to maintain, but either is fine if you size it deliberately.

---

### Q66. How can you reduce startup time: lazy init, CDS and GraalVM native image? [Advanced]

**Level: Advanced**

**Short answer:** First find what is slow. Cheap options are lazy initialisation, fewer auto-configurations and a smaller classpath. Class Data Sharing (CDS) speeds up class loading. GraalVM native image compiles the app ahead of time for very fast startup and low memory, but with trade-offs.

**Explanation:**

- **Measure first:** use `--debug` and the Actuator `startup` endpoint (with `BufferingApplicationStartup`) to see the slowest beans. Often the cost is a Flyway migration, a remote config call, a big component scan or an eager cache load, not Spring itself.
- **Lazy initialisation:** `spring.main.lazy-initialization=true` creates beans on first use. Startup is faster, but first requests are slower and wiring errors show up later. Use with care in production.
- **CDS (Class Data Sharing):** stores loaded class data in an archive so the JVM starts faster. Boot 3.3+ has support for it (check docs for your version). Needs a training run step in the image build.
- **Other levers:** exclude unused auto-configurations, avoid classpath scanning of huge packages, use `proxyBeanMethods = false` in your configs, upgrade the JDK.
- **Virtual threads, CRaC:** not startup tools by themselves. CRaC (checkpoint and restore) is supported by Boot 3.2+ on JVMs that offer it.
- **GraalVM native image** (Boot 3 has first-class support through Spring AOT processing):
  - Start in milliseconds, much lower memory. Good for serverless, scale-to-zero, many small services.
  - Trade-offs: slow and heavy build, closed-world assumptions (reflection, proxies and resources must be known at build time, with hints), some libraries are not compatible, harder debugging, and peak throughput can be lower than a warmed-up JIT.
  - For long-running services on Kubernetes, a normal JVM is often good enough. Decide from measurements.

```bash
# Build a native image with the Boot plugin (needs GraalVM)
./mvnw -Pnative native:compile

# Or build a native container image with buildpacks
./mvnw -Pnative spring-boot:build-image
```

```yaml
spring:
  main:
    lazy-initialization: true
```

**Follow-up questions:**

- **JIT vs AOT?**
  JIT compiles hot code while the app runs and can optimise using real data. AOT compiles before running, so it starts fast but cannot adapt.
- **Do all Spring libraries work in native image?**
  Most mainstream ones do, but check each. Hibernate, Kafka client and Spring Security are supported, with caveats. Test it.
- **Why not make every service native?**
  Build time, CI cost, debugging difficulty and compatibility. Use it where startup and memory matter most.

---

### Q67. Scenario: one endpoint is slow in production. How do you debug it? [Advanced]

**Level: Advanced**

**Short answer:** Measure before guessing. Find where the time goes with metrics and traces, then confirm with the database (slow queries, N+1) or a thread dump or profiler. Fix the cause, then check the metric again.

**Explanation:**

Example: `GET /api/orders?customerId=...` has a p99 of 8 seconds since yesterday.

```text
1. Scope it      Which endpoint, which instances, since when? (deploy? traffic? data growth?)
2. Metrics       p50/p95/p99 and rate for this endpoint (Grafana). One pod or all?
3. Trace         Open a slow trace. Which span is long: DB, Inventory call, Kafka, our code?
4. Database      Slow query log, EXPLAIN. Many identical queries = N+1 (Q26). Missing index. Large page size.
5. Downstream    Slow Inventory-Service? Timeout too long? Retries multiplying time? (Q53)
6. Resources     Hikari pending connections (Q34), Tomcat busy threads, CPU, GC pauses.
7. Thread dump   3 dumps, 5 seconds apart: where are threads stuck? Lock waits? Waiting for connections?
8. Profile       async-profiler or JFR for CPU hot spots (JSON serialisation, regex, big loops).
9. Fix + verify  Change one thing, compare the metrics, add a regression test or alert.
```

Quick tools:

```bash
# Thread dump (inside the container, or via /actuator/threaddump)
jcmd <pid> Thread.print

# Short JFR recording
jcmd <pid> JFR.start duration=60s filename=/tmp/slow.jfr

# MySQL: see current queries and the slow log
SHOW FULL PROCESSLIST;
EXPLAIN SELECT * FROM orders WHERE customer_id = 42 AND status = 'NEW';
```

Typical root causes: N+1 queries, missing index after data growth, `Page` count query on a huge table, lock waits, a slow downstream with no timeout, big responses (no pagination), cache not working (miss every time), GC pressure, connection pool waits, JSON serialisation of big graphs.

**Follow-up questions:**

- **Average is fine but p99 is bad. Why?**
  A subset of requests hits a slow path: large customers, cache misses, lock waits, GC pauses, retries. Look at slow traces only.
- **How do you find N+1 in production?**
  A trace with many repeated identical DB spans, or Hibernate statistics showing a high statement count per request.
- **What if you cannot reproduce it locally?**
  Use production-like data volume, copy a slow query's parameters, and use tracing with sampling raised for a short time.

---

### Q68. How do you debug a memory leak, connection pool exhaustion and thread starvation? [Advanced]

**Level: Advanced**

**Short answer:** Each has clear signals. Memory leak: heap after full GC keeps growing, then OutOfMemoryError. Pool exhaustion: Hikari timeout errors and high pending count. Thread starvation: all request threads blocked or waiting, latency rises, CPU stays low.

**Explanation:**

**1. Memory leak**

- Signals: `jvm_memory_used_bytes` for old gen rises after each GC, GC time rises, pod restarts with OOM.
- Capture: start with `-XX:+HeapDumpOnOutOfMemoryError`, or run `jcmd <pid> GC.heap_dump /tmp/heap.hprof`. Open it in Eclipse MAT and look at the "dominator tree" and "leak suspects".
- Common Spring causes: unbounded in-memory cache or static `Map`, `ThreadLocal` not cleared on pooled threads, listeners never removed, big result sets loaded into memory, huge persistence context in a long job, high-cardinality Micrometer tags.
- Do not only raise `-Xmx`. It delays the crash. Also check that it is heap and not native memory (direct buffers, threads, metaspace), and not the container limit (Q65).

**2. Connection pool exhausted**

- Error: `HikariPool-1 - Connection is not available, request timed out after 30000ms`.
- Metrics: `hikaricp_connections_active` equals max, `hikaricp_connections_pending` greater than 0.
- Find who holds connections: thread dump shows many threads inside JDBC calls or waiting in `HikariPool.getConnection`. Set `leak-detection-threshold` to log where a connection was taken and not returned.
- Causes and fixes: see the table in Q34 (long transactions, remote calls inside a transaction, OSIV, REQUIRES_NEW nesting, slow queries).

**3. Thread starvation**

- Tomcat has 200 request threads by default. If a downstream call has no timeout and the downstream is slow, threads pile up waiting. When all 200 are busy, even `/actuator/health` can time out.
- Signals: `tomcat_threads_busy_threads` at the max, low CPU, many threads in `WAITING` or `SocketRead` in the dump.
- Fixes: timeouts on every client call, circuit breaker and bulkhead (Q52, Q53), separate pools for slow work, async or virtual threads where it fits, and load shedding. Check also `@Async` pools with an unbounded queue (Q39), and parallel streams or `CompletableFuture` using the shared `ForkJoinPool.commonPool` for blocking work.
- Deadlock: the thread dump shows "Found one Java-level deadlock".

```bash
# Quick tools
jcmd <pid> GC.heap_dump /tmp/heap.hprof
jcmd <pid> Thread.print > /tmp/threads.txt
jcmd <pid> GC.class_histogram | head -30
kubectl top pod order-service-7d9c
kubectl describe pod order-service-7d9c   # look for OOMKilled and restarts
```

**Follow-up questions:**

- **How do you tell heap OOM from container OOMKilled?**
  Heap OOM prints `java.lang.OutOfMemoryError` in the logs. OOMKilled shows in `kubectl describe` with exit code 137 and no Java error.
- **Why can CPU be low while the service is overloaded?**
  Threads are waiting (IO, locks, pool), not computing.
- **What helps find a leak without a heap dump?**
  `jcmd GC.class_histogram` over time shows which class instance counts keep growing. JFR also has allocation events.

---

### Q69. Scenario: 5xx errors spike after a release, or the app starts slowly. What do you do? [Advanced]

**Level: Advanced**

**Short answer:** First reduce impact (roll back or scale if the cause is a recent change), then find the cause with metrics, logs and traces. For slow startup, find the slowest phase using startup metrics and logs.

**Explanation:**

**5xx spike checklist:**

```text
1. Impact     Which endpoints and status codes (500 / 502 / 503 / 504)? What share of traffic?
2. Change?    Did it start with a deploy, config change, DB migration or traffic change? If yes: roll back first, analyse after.
3. Where      Gateway or load balancer (502/504) vs app (500)? One pod or all?
4. Logs       Group errors by exception and trace ID. Find the first error, not the loudest.
5. Dependency DB (Hikari timeouts), Inventory-Service (timeouts), Kafka, Redis? Circuit breaker states?
6. Resources  Pod restarts (OOMKilled), CPU throttling, GC pauses, thread pool saturation.
7. Readiness  Are unready pods still receiving traffic? Is graceful shutdown working?
8. Mitigate   Roll back, scale out, disable a feature flag, open circuit, add rate limit.
9. Follow up  Root cause, regression test, alert that would have caught it earlier, short post-incident review.
```

Meaning of codes: 500 = unhandled error in the app. 502 = proxy got a bad response (pod down or crashed). 503 = no ready pod or overloaded. 504 = proxy timed out waiting for the app.

**Slow or failing startup checklist:**

- Read the log timeline. Enable the Actuator `startup` endpoint (`BufferingApplicationStartup`) to see slow beans.
- Common causes: Flyway migration on startup, a remote call in `@PostConstruct` or config import (Config Server, Vault), cache warm-up, large component scan, DNS or network wait, resource limits too low (CPU throttling slows startup a lot), Hibernate schema validation on big schemas, Kafka listener connecting to an unreachable broker.
- Kubernetes: a `startupProbe` with a larger `failureThreshold` prevents the liveness probe from killing a slow starter in a restart loop. A CrashLoopBackOff usually means an exception at startup. Read `kubectl logs --previous`.
- Fix options: move migrations to a separate job, lazy-load heavy things, raise CPU limits, see Q66.
- Bean errors: `--debug` conditions report, `NoSuchBeanDefinitionException` (component scan or missing condition), and circular dependency errors (Q10).

```bash
kubectl logs order-service-7d9c --previous
kubectl describe pod order-service-7d9c
kubectl rollout undo deployment/order-service
```

**Follow-up questions:**

- **Roll back or fix forward?**
  If the cause is clearly the last release and rollback is safe (no incompatible DB migration), roll back first. Restore service, then investigate.
- **What makes a DB migration rollback-unsafe?**
  Dropping or renaming columns the old version needs. Use expand and contract (Q31).
- **How do you prevent repeat incidents?**
  Canary or rolling releases with automatic rollback on error-rate alerts, load and soak tests, and readiness checks that reflect real health.

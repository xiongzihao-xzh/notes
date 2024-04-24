Spring Cloud OpenFeign 是一种基于 Spring  Cloud 的声明式 REST 客户端，它简化了与 HTTP 服务交互的过程。它将 REST 客户端的定义转化为 Java 接口，并且可以通过注解的方式来声明**请求参数、请求方式、请求头等**信息，从而使得客户端的使用更加方便和简洁。同时，它还提供了**负载均衡**和**服务发现**等功能，可以与 Eureka、Consu l等**注册中心集成**使用

# 如何使用 Feign

1. 使用 `@EnableFeignClients` 开启服务
2. 使用 `@FeignClient` 注解在接口上

ApplicationContext 中的 Bean 名称是接口的完全限定名称。要指定你自己的别名值，你可以使用 `@FeignClient` 注解的 `qualifiers` 值

负载均衡器客户端发现 `name` 的物理地址，它将在服务注册中心解析该服务。如何不使用注册中心，也可以使用 `SimpleDiscoveryClient` 在外部配置中配置一个服务列表

Spring Cloud OpenFeign 支持 Spring Cloud LoadBalancer 阻塞模式的所有功能

> 要在 `@Configuration` 注解的类上使用 @`EnableFeignClients` 注解，请确保指定客户端的位置，例如： `@EnableFeignClients(basePackages = "com.example.clients")` 或明确列出它们： `@EnableFeignClients(clients = InventoryServiceFeignClient.class)`

# 覆盖 Feign 的默认值

Spring Cloud 的 `Feign` 支持中的一个核心概念是命名的客户端。每个feign客户端都是一个组件集合的一部分，这些组件一起工作以按需联系远程服务器，并且该集合有一个你作为应用开发者使用 `@FeignClient` 注解给它的名字。Spring Cloud 使用 `FeignClientsConfiguration` 为每个命名的客户端按需创建一个新的组合作为 `ApplicationContext`。这包括（除其他外）一个 `feign.Decoder`、一个 `feign.Encoder` 和一个 `feign.Contract`。通过使用 `@FeignClient` 注解的 `contextId` 属性，可以重写该集合的名称

> 使用 `@FeignClient` 注解的 `contextId` 属性，除了改变 `ApplicationContext` 集合的名称外，它还将覆盖客户端名称的别名，它将被用作为该客户端创建的配置Bean名称的一部分

# 超时处理

我们可以在默认客户端和命名客户端上配置超时。`OpenFeign` 使用两个超时参数：

* `connectTimeout` 防止因服务器处理时间过长而阻塞调用者
* `readTimeout` 从连接建立时开始应用，当返回响应的时间过长时就会被触发

# 手动创建 Feign Client

```java
@Import(FeignClientsConfiguration.class)
class FooController {

    private FooClient fooClient;

    private FooClient adminClient;

    @Autowired
    public FooController(Client client, Encoder encoder, Decoder decoder, Contract contract, MicrometerObservationCapability micrometerObservationCapability) {
        this.fooClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .addCapability(micrometerObservationCapability)
                .requestInterceptor(new BasicAuthRequestInterceptor("user", "user"))
                .target(FooClient.class, "https://PROD-SVC");

        this.adminClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .addCapability(micrometerObservationCapability)
                .requestInterceptor(new BasicAuthRequestInterceptor("admin", "admin"))
                .target(FooClient.class, "https://PROD-SVC");
    }
}
```

# Fallback

`@FeignClient` 启用 fallback，将 `fallback` 属性设置为实现 fallback 的类名

```java
@FeignClient(name = "test", url = "http://localhost:${server.port}/", fallback = Fallback.class)
    protected interface TestClient {

        @RequestMapping(method = RequestMethod.GET, value = "/hello")
        Hello getHello();

        @RequestMapping(method = RequestMethod.GET, value = "/hellonotfound")
        String getException();

    }

    @Component
    static class Fallback implements TestClient {

        @Override
        public Hello getHello() {
            throw new NoFallbackAvailableException("Boom!", new RuntimeException());
        }

        @Override
        public String getException() {
            return "Fixed response";
        }

    }
```

如果需要访问使 fallback 触发的原因，可以使用 `@FeignClient` 里面的 `fallbackFactory` 属性

```java
@FeignClient(name = "testClientWithFactory", url = "http://localhost:${server.port}/",
            fallbackFactory = TestFallbackFactory.class)
    protected interface TestClientWithFactory {

        @RequestMapping(method = RequestMethod.GET, value = "/hello")
        Hello getHello();

        @RequestMapping(method = RequestMethod.GET, value = "/hellonotfound")
        String getException();

    }

    @Component
    static class TestFallbackFactory implements FallbackFactory<FallbackWithFactory> {

        @Override
        public FallbackWithFactory create(Throwable cause) {
            return new FallbackWithFactory();
        }

    }

    static class FallbackWithFactory implements TestClientWithFactory {

        @Override
        public Hello getHello() {
            throw new NoFallbackAvailableException("Boom!", new RuntimeException());
        }

        @Override
        public String getException() {
            return "Fixed response";
        }

    }
```

# Feign 和 @Primary

Spring Cloud OpenFeign 将所有Feign实例标记为 `@Primary`

# Feign 继承的支持

Feign 通过单继承接口支持模板式的api。这允许将常见的操作分组到方便的基础接口中

```java
public interface UserService {

    @RequestMapping(method = RequestMethod.GET, value ="/users/{id}")
    User getUser(@PathVariable("id") long id);
}

@RestController
public class UserResource implements UserService {

}

package project.user;

@FeignClient("users")
public interface UserClient extends UserService {

}
```

> `@FeignClient` 接口不应该在服务器和客户端之间共享，并且不再支持在类级别上用 `@RequestMapping` 注解 `@FeignClient` 接口

# Feign request/response 压缩

```
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.response.enabled=true
```

# Feign 日志

Feign 的日志只响应 `DEBUG` 级别

```yaml
logging.level.project.user.UserClient: DEBUG
```

`Logger.Level` 对象，告诉 Feign 要记录多少内容 

* `NONE`, 没日志（**默认**）
* `BASIC`, 只记录请求方法和URL以及响应状态代码和执行时间
* `HEADERS`, 记录基本信息以及请求和响应头
* `FULL`, 记录请求和响应的header、正文和元数据

```java
@Configuration
public class FooConfiguration {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```




























Springboot-3.0 已经放弃了 spring.factories 自动装配，它被 /resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports 所取代
# 解决方案 1
在 resources 目录下添加 META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports 文件，写入
```properties
org.apache.rocketmq.spring.autoconfigure.RocketMQAutoConfiguration
```
# 解决方案2
使用 `@Import(RocketMQAutoConfiguration.class)` 注解导入 `RocketMQAutoConfiguration` 配置类
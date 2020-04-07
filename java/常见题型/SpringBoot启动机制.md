## SpringBoot启动机制

- 怎么整合第三方依赖？

  maven父集成

- 怎么做到无配置文件集成的SpringMVC

  @SpringBootApplication组成

  - @SpringBootConfiguration

    本质就是@Configuration，其实就是一个Spring容器的配置类

  - @ComponentScan

    自动扫描并加载符合条件的组件或者bean定义，最终将这些定义

  - @EnableAutoConfiguration

    借助@Import的支持，收集和注册特定场景相关的bean定义(重点)

  使用@EnableWebMvc集成SpringMVC功能

- Tomcat是怎么启动的

  通过@EnableAutoConfiguration中Import注解加载(spring-boot-autoconfiguration spring.factories)，位于EmbeddedWebServerFactoryCustomizerAutoConfiguration中引入Tomcat.class


---
title: SpringBoot各种注解
date: 2024-8-20 17:24:12
tags: 
	- springboot
	- 注解
categories: java
top_img: /images/bg2.jpg
---



## 1. 底层注解

以下是统一格式的注解说明，涵盖 Spring Boot 中的主要注解及其用途、示例和配置：

### 1.1 基础注解

#### `@SpringBootApplication`

- **说明**: 这是一个复合注解，包含了 `@Configuration`、`@EnableAutoConfiguration` 和 `@ComponentScan`。它是启动 Spring Boot 应用的入口注解。

- **示例**:
  ```java
  @SpringBootApplication
  public class MyApplication {
      public static void main(String[] args) {
          SpringApplication.run(MyApplication.class, args);
      }
  }
  ```

---

#### `@Configuration`

- **说明**: 用于定义 Spring 配置类，表示该类包含一个或多个 `@Bean` 方法，用于定义 Bean。

- **示例**:
  ```java
  @Configuration
  public class AppConfig {
      @Bean
      public MyService myService() {
          return new MyService();
      }
  }
  ```

---

#### `@Component`

- **说明**: 表示一个 Spring 管理的组件，通常用于自动扫描和注册 Bean。

- **示例**:
  ```java
  @Component
  public class MyComponent {
      // Component implementation
  }
  ```

---

#### `@Bean`

- **说明**: 用于在配置类中定义一个 Bean。

- **示例**:
  ```java
  @Configuration
  public class AppConfig {
      @Bean
      public MyService myService() {
          return new MyService();
      }
  }
  ```

---

### 2.1 配置属性注解

#### `@EnableConfigurationProperties`

- **说明**: 启用对 `@ConfigurationProperties` 注解的支持，允许 Spring Boot 自动将配置文件中的属性映射到 Bean 的字段中。

- **示例**:
  ```java
  @SpringBootApplication
  @EnableConfigurationProperties(MyProperties.class)
  public class MyApplication {
      public static void main(String[] args) {
          SpringApplication.run(MyApplication.class, args);
      }
  }

  @Component
  @ConfigurationProperties(prefix = "my")
  public class MyProperties {
      private String name;
      private int age;

      // getters and setters
  }
  ```

  配置文件 `application.properties`：
  ```properties
  my.name=John Doe
  my.age=30
  ```

---

#### `@ConfigurationProperties`

- **说明**: 用于将配置文件中的属性绑定到 Java 类上，通常和 `@EnableConfigurationProperties` 一起使用。

- **示例**:
  ```java
  @Component
  @ConfigurationProperties(prefix = "my")
  public class MyProperties {
      private String name;
      private int age;

      // getters and setters
  }
  ```

  配置文件 `application.properties`：
  ```properties
  my.name=John Doe
  my.age=30
  ```

---

### 3. 1条件化配置注解

#### `@Condition`

- **说明**: `@Condition` 是一个用于条件化配置的注解。它通常配合 `@Conditional` 使用，用于在特定条件下启用或禁用配置类或 Bean。

- **示例**:
  ```java
  @Configuration
  @ConditionalOnProperty(name = "my.feature.enabled", havingValue = "true")
  public class FeatureConfiguration {
      @Bean
      public MyFeature myFeature() {
          return new MyFeature();
      }
  }
  ```

  配置文件中的条件：
  ```properties
  my.feature.enabled=true
  ```

---

#### `@Import`

- **说明**: 用于将其他配置类导入当前配置类中。这使得可以在不同的配置类中组织和管理 Bean 定义。

- **示例**:
  ```java
  @Configuration
  @Import(AdditionalConfig.class)
  public class MainConfig {
      // Main configuration
  }

  @Configuration
  public class AdditionalConfig {
      @Bean
      public AnotherService anotherService() {
          return new AnotherService();
      }
  }
  ```

---

#### `@AutoConfiguration`

- **说明**: 这是 Spring Boot 的核心注解之一，自动配置类可以帮助 Spring Boot 根据类路径中的库、项目的其他 Bean 定义等来自动配置应用程序。

- **示例**: 通常不需要显式使用该注解，因为 Spring Boot 启动类中的 `@SpringBootApplication` 注解已经包含了 `@EnableAutoConfiguration`。

---

### 4.1 条件性创建 Bean

#### `@Conditional` 和 `@ConditionalOnProperty`

- **说明**: `@Conditional` 是一个更底层的注解，允许基于不同条件创建 Bean。`@ConditionalOnProperty` 是常用的条件注解之一，用于基于配置文件中的属性来条件性地创建 Bean。

- **示例**:
  ```java
  @Configuration
  @ConditionalOnProperty(name = "my.feature.enabled", havingValue = "true", matchIfMissing = true)
  public class ConditionalConfig {
      @Bean
      public ConditionalService conditionalService() {
          return new ConditionalService();
      }
  }
  ```

---

以上注解是 Spring Boot 中常用的底层注解，涵盖了应用启动、配置、条件化和组件管理等功能。

### 2. IOC 注解

这些注解用于控制反转（IoC），主要涉及 Bean 的管理和依赖注入。

- **`@Autowired`**  
  
  - **说明**: 自动注入依赖项。Spring 会根据类型自动注入对应的 Bean。
  - **示例**:
    ```java
    @Component
    public class MyComponent {
        @Autowired
        private MyService myService;
        
        // use myService
    }
    ```
  ```
  
  ```
  
- **`@Qualifier`**  
  
  - **说明**: 与 `@Autowired` 一起使用，指定要注入的具体 Bean，解决多个 Bean 的歧义问题。
  - **示例**:
    ```java
    @Component
    public class MyComponent {
        @Autowired
        @Qualifier("specificService")
        private MyService myService;
        
        // use myService
    }
    ```
  ```
  
  ```
  
- **`@Resource`**  
  
  - **说明**: 根据名称注入 Bean，主要用于根据 Bean 的名字注入。
  - **示例**:
    
    ```java
    @Component
    public class MyComponent {
        @Resource(name = "myService")
        private MyService myService;
        
        // use myService
  }
    ```
  
- **`@PostConstruct`**  
  
  - **说明**: 用于标记一个初始化方法，在依赖注入完成后调用。
  - **示例**:
    
    ```java
    @Component
    public class MyComponent {
        @PostConstruct
        public void init() {
            // initialization logic
        }
    }
    ```
  
- **`@PreDestroy`**  
  
  - **说明**: 用于标记一个销毁方法，在 Bean 被销毁之前调用。
  - **示例**:
    ```java
    @Component
    public class MyComponent {
        @PreDestroy
        public void destroy() {
            // cleanup logic
        }
    }
    ```

### 3. Controller 参数注解

这些注解主要用于处理请求并传递参数给控制器的方法。

- **`@RestController`**  
  - **说明**: 是 `@Controller` 和 `@ResponseBody` 的组合，表示这个类是一个控制器，并且所有的方法都以 JSON 或 XML 的形式返回数据。
  - **示例**:
    
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @GetMapping("/hello")
        public String hello() {
            return "Hello, World!";
        }
    }
    ```
  ```
  
  ```
  
- **`@RequestMapping`**  
  - **说明**: 用于定义请求的 URL 路径和方法类型（GET、POST 等）。
  - **示例**:
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @RequestMapping(value = "/greet", method = RequestMethod.GET)
        public String greet() {
            return "Greetings!";
        }
    }
    ```

- **`@GetMapping`**  
  - **说明**: 用于处理 HTTP GET 请求的快捷方式。
  - **示例**:
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @GetMapping("/hello")
        public String hello() {
            return "Hello, World!";
        }
    }
    ```

- **`@PostMapping`**  
  - **说明**: 用于处理 HTTP POST 请求的快捷方式。
  - **示例**:
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @PostMapping("/submit")
        public String submit(@RequestBody MyData data) {
            // handle data
            return "Data submitted!";
        }
    }
    ```

- **`@RequestParam`**  
  
  - **说明**: 用于从请求参数中提取值，并将其绑定到方法参数。
  - **示例**:
    
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @GetMapping("/greet")
        public String greet(@RequestParam(name = "name", defaultValue = "Guest") String name) {
            return "Hello, " + name + "!";
        }
  }
    ```
  
- **`@RequestPart`** 是 Spring Framework 中用于处理 multipart/form-data 请求的注解，通常用于文件上传或复杂的表单数据。它通常与 `@PostMapping` 或 `@RequestMapping` 一起使用，用于将请求体中的部分数据绑定到方法参数上。这个注解使得处理文件和表单数据变得更加简单和直接。
  
  ### 使用场景
  
  1. **文件上传**: `@RequestPart` 可以用来绑定上传的文件。
  2. **复杂表单数据**: 如果请求体包含多个部分，例如文件和 JSON 数据，`@RequestPart` 可以用来处理这些不同的部分。
  
  ### 示例代码
  
  假设我们有一个上传文件和一些其他表单数据的场景。我们可以定义一个 REST 控制器方法来处理这个请求。
  
  #### 上传文件和额外的表单数据
  
```java
  javaimport org.springframework.web.bind.annotation.PostMapping;
  import org.springframework.web.bind.annotation.RequestPart;
  import org.springframework.web.bind.annotation.RestController;
  import org.springframework.web.multipart.MultipartFile;
  import org.springframework.web.bind.annotation.RequestParam;
  
  @RestController
  public class FileUploadController {
  
      @PostMapping("/upload")
      public String handleFileUpload(
              @RequestPart("file") MultipartFile file,
              @RequestPart("description") String description) {
          
          // 处理文件
          String fileName = file.getOriginalFilename();
          long fileSize = file.getSize();
  
          // 处理描述
          System.out.println("Description: " + description);
          
          // 返回响应
          return "File " + fileName + " with size " + fileSize + " and description " + description + " uploaded successfully!";
      }
  }
```

- **`@PathVariable`**  
  
  - **说明**: 用于从 URL 路径中提取值，并将其绑定到方法参数。
  - **示例**:
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @GetMapping("/user/{id}")
        public String getUser(@PathVariable String id) {
            return "User ID: " + id;
        }
    }
    ```
  ```
  
  ```
  
- **`@RequestBody`**  
  
  - **说明**: 用于将请求体中的数据绑定到方法参数上，通常用于处理 POST 请求中的 JSON 数据。
  - **示例**:
    ```java
    @RestController
    @RequestMapping("/api")
    public class MyController {
        @PostMapping("/create")
        public String create(@RequestBody MyData data) {
            // handle data
            return "Data created!";
        }
    }
    ```
  ```
  
  ```
  
- **`@ResponseBody`**  
  - **说明**: 表示方法的返回值应该直接绑定到 HTTP 响应体中，通常用于返回 JSON 或 XML 数据。
  - **示例**:
    ```java
    @RestController
    public class MyController {
        @GetMapping("/data")
        @ResponseBody
        public MyData getData() {
            return new MyData();
        }
    }
    ```

### 4. Spring MVC与Spring Boot的区别

**Spring MVC** 和 **Spring Boot** 都是Spring框架的一部分，但它们各自的用途和功能有所不同。

- **Spring MVC**:
  - **定义**: 是Spring框架的一部分，主要用于构建Web应用程序。它基于MVC（Model-View-Controller）设计模式，提供了一个分层的架构来开发Web应用。
  - **功能**: 提供了控制器、视图解析器、数据绑定等功能。它专注于Web层的开发，并且需要开发者自行配置各种组件和设置。
  - **配置**: 配置通常较为繁琐，涉及大量的XML配置文件或者Java配置类。
  - **应用**: 适用于需要灵活控制的传统Web应用项目，特别是那些需要手动配置各种组件的项目。

- **Spring Boot**:
  - **定义**: 是一个基于Spring的开源框架，用于简化Spring应用的创建和部署。它提供了一种开箱即用的解决方案，减少了大量的配置和样板代码。
  - **功能**: 提供了自动配置、内嵌服务器（如Tomcat、Jetty）、生产级别的特性（如监控和管理），使开发人员可以专注于业务逻辑，而不必担心基础设施的配置。
  - **配置**: 提供了默认配置和自动配置，减少了手动配置的需求。使用`application.properties`或`application.yml`文件进行配置，使得项目设置更加简洁。
  - **应用**: 适用于快速开发和部署，尤其是微服务架构和新建项目，因为它可以减少项目启动时间和开发过程中的配置复杂性。

### 5. @Autowired, @Qualifier 和 @Resource的区别

这些注解都是用于依赖注入的，但它们在使用和功能上有所不同：

- **@Autowired**:
  - **定义**: 是Spring框架提供的注解，用于自动注入Bean。
  - **功能**: 自动根据类型注入Bean。如果Spring容器中有多个符合条件的Bean，可能会导致`NoUniqueBeanDefinitionException`异常。
  - **使用**: 可以用于构造函数、方法、字段等。可选地结合`@Qualifier`使用，以指定注入的具体Bean。

- **@Qualifier**:
  - **定义**: 是Spring框架提供的注解，用于在使用`@Autowired`时，指定注入的Bean的名称。
  - **功能**: 解决`@Autowired`在存在多个候选Bean时的冲突问题。通过指定Bean的名称或ID，明确注入的目标。
  - **使用**: 必须和`@Autowired`一起使用，以提供明确的注入目标。

- **@Resource**:
  - **定义**: 是Java EE（也称为Jakarta EE）提供的注解，属于JSR-250规范的一部分，也可以在Spring中使用。
  - **功能**: 根据名称进行注入。它首先会尝试按名称注入，如果找不到对应的名称，则会按类型进行注入。
  - **使用**: 更偏向于按名称注入，并且不需要和其他注解（如`@Qualifier`）一起使用。它的行为与`@Autowired` + `@Qualifier`组合有一定相似性，但默认按名称注入，适用于需要根据名称进行注入的场景。

**总结**:
- **@Autowired**: 按类型自动注入，可以和`@Qualifier`配合使用。
- **@Qualifier**: 用于指定具体的Bean，配合`@Autowired`使用。
- **@Resource**: 按名称进行注入（如果没有找到按名称的Bean，则按类型进行注入）。

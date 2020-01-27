# Spring Boot 学习笔记

## 1.配置文件

### 1.1 配置文件映射组件

SpringBoot的全局配置文件：

- application.properties

- application.yml

  使用示例：

  ```java
  /*
      可以将配置文件（application.yml）中配置的每一个属性的值映射到组件中
      (prefix = "person")：将配置文件中person 属性一一映射
      @ConfigurationProperties 可以进行JSR303 数据校验，例如使用 @Email 可以校验邮箱地址
   */
  @Component
  @ConfigurationProperties(prefix = "person")
  public class Person {
  
  //    /*
  ////        只在获取单个属性值的时候使用 @Value
  ////     */
  ////    @Value("${person.name}")
  
      private String name;
      private Integer age;
  
      private Map map;
  
      private Dog dog;
      
      @Override
      public String toString() {
          return "Person{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  ", map=" + map +
                  ", dog=" + dog +
                  '}';
      }
  
  }
  ```

  ```yaml
  person:
    name: "Jerry${random.uuid}" #占位符 采用 ${} 来实现，此处代表一个随机数
    age: 23
    country: China
    map: {k1: v1 ,k2: v2}
    dog:
      name: '${person.name}Mary' # 此处取得 person下的name值
      age: 3
  ```

  可以输出Person对象查看具体数据

  ### 1.2 多环境支持

  在开发测试和发布时，使用环境不同，也要使用不同的配置文件

  #### 1.2.1多个Profile文件

  一般配置三种环境

  - application.properties

  - application-dev.properties

  - application-prod.properties

    ```properties
    server.port=8080
    spring.profiles.active=dev # 使用此语句激活dev环境的配置文件
    ```

  #### 1.2.2 yaml 文件多文档块

  如下编写yml文件，使用“---”进行分隔，使用spring.profiles.active来激活

  如果配置很多，也可以分开写

  ```yaml
  server:
    port: 8081
  
  spring:
    profiles:
      active: dev
  ---
  server:
    port: 8082
  spring:
    profiles: prod
  ---
  server:
    port: 8083
  spring:
    profiles: dev
  ```

  #### 1.2.3 激活方式

  - 配置文件中 使用 spring.profiles.active 激活
  - 命令行参数：
    - --spring.profiles.active=dev 填入IDE 当中的 Program arguments中
    - 或者打包后在shell中填入启动参数
  - 虚拟机参数：
    - -Dspring.profiles.active=dev

  ### 1.3 配置文件的加载位置

  以下位置的 application.properties 和 application.yml文件被扫描并作为默认配置文件

  - -file:../config/
  - -file:../
  - -classpath:/config/
  - -classpath:/

  优先级从高到低，高优先级覆盖低优先级

  **可以通过 spring.config.location 改变默认的配置文件位置**（打包后上云经常使用）

  具体外部配置文件加载顺序参考官方文档。。。

  ### 1.4 配置文件原理

  [配置文件能配置的属性参照](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/appendix-application-properties.html#core-properties)

  **自动配置原理：**

  之后看源码。。。

  1）SpringBoot 启动时加载主配置类，开启自动配置功能 @EnableAutoConfiguration

  2）@Enable

  

  ## 2.SpringBoot与日志

  ### 2.1日志框架

  

  

  

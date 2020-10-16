# 1. SpringBoot的配置文件格式

`SpringBoot` 的配置文件支持 `properties和yml`，甚至他还支持json。

> 更推荐使用yml文件格式：
>
> 1. yml文件，会根据换行和缩进帮助管理配置文件所在位置
> 2. yml文件，相比properties更轻量级一些
>
> yml文件的劣势：
>
> 1. 严格遵循换行和缩进
> 2. 在填写value时，一定要在：后面跟上空格

# 2. 环境配置

1. 在`application.yml`文件中添加一个配置项：

   ```
   spring:
     profiles:
       active: 环境名(例：dev)
   ```

2. 在resource目录下，创建多个`application-环境名.yml`文件即可
3. 在部署工程时，通过`Java -jar jar文件 --spring.profiles.active="环境名(dev)"` 

> 注意：打包：`mvn clean package`

# 3. 引入外部配置文件信息

1. 和传统的SSM方式一样，通过@Value的注解去获取properties/yml文件中的内容

- yml文件内容：

  ```Java
  picPath: D:\xxx\yyy
  ```

  ```Java
  @Value("${picPath}")
  private String picPath;
  
  @GetMapping("/picPath")
  private String picPath() {
    return picPath;
  }
  ```

2. 如果在yml文件中需要编写大量的自定义配置，并且具有统一的前缀时，采用如下方式：

   - yml文件内容

   ```
   aliyun:
     xxxx: xxxxxx;
     yyyy: yyyyyy;
     ...
   ```

   - 将AliyunProperties添加到spring容器

   ```Java
   @ConfigurationProperties(prefix = "aliyun")
   @Component
   @Data
   public class AliyunProperties {
       private String xxxx;
       private String yyyy;
     	...
   }
   ```

   - 使用

   ```Java
   @Autowired
   private AliyunProperties properties;
   
   @GetMapping("/aliyun")
   private AliyunProperties aliyun() {
     return properties;
   }
   ```

# 4. 热加载

1. 导入依赖

   ```xml
   <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
     <optional>true</optional>
   </dependency>
   ```

2. 修改setting中的配置

   setting -> Build,.... ->Complier -> Build project automatically
# 1. @Configuration和@Bean

> 之前在使用SSM去开发时，在XML文件中编写bean标签
>
> 但是springboot不推荐使用xml文件
>
> @configuration注解相当于beans标签
>
> @Bean注解相当于bean标签
>
> ​	id="方法名 | Bean注解中的name属性（优先级更高）"
>
> ​	class="方法的返回结果"

```Java
@Configuration     //代表当前类是一个配置类
public class UserConfig {

    @Bean(name="user1")    //构建一个实例，放到spring容器中

    public User user() {
        User user = new User();
        user.setId(1);
        user.setName("aaa");
        return user;
    }

    /*
    <beans>    @Configuration
        <bean id="user1" class="com.example.springboot01hello.entity.User"/>
    </beans>
    */
}
```

# 2. @SpringBootApplication

`@SpringBootApplication`是一个组合注解

1. `@SpringBootConfiguration` 就是 `@Configuration` 注解，代表启动类就是一个配置类。

2. `@EnableAutoConfiguration` 帮助实现自动装配，SpringBoot工程启动时，运行一个`SpringFactoriesLoader` 的类，加载 `META-INF/spring.factories`配置类（已经开启的），通过 ``SpringFactoriesLoader`  中的`load`方法，以for循环的方式，一个一个加载。

   > 好处：无需编写大量的整合配置信息，只需要按照SpringBoot提供好的约定去整合即可
   >
   > 坏处：如果说你导入了一个starter依赖，那么你就需要填写他必要的配置信息。
   >
   > 手动关闭自动装配指定内容：`@SpringBootApplication(exclude = QuartzAutoConfiguration.class)`

3. `@ComponentScan` 相当于 `<context:component-scan basePackage="包名"/>`，帮助扫描注解的。




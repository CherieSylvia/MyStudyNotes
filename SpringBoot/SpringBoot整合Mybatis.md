# 1. xml方式整合mybatis

1. 导入依赖

   ```xml
   <!--        mysql驱动-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency>
   
   <!--        druid连接-->
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>druid-spring-boot-starter</artifactId>
               <version>1.1.20</version>
           </dependency>
   
   <!--        mybatis-->
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>1.3.2</version>
           </dependency>
   ```

2. 编写配置文件

   - 准备实体类

     ```Java
     @Data
     public class District {
     
       private long id;
       private String username;
       private String password;
       private long role;
       private long status;
       private java.sql.Timestamp createTime;
       private java.sql.Timestamp updateTime;
     }
     ```

     ```Java
     @Data
     public class Air {
     
       private long id;
       private String content;
       private long type;
       private long state;
       private String publisherUsername;
       private long publisherId;
       private long receiverId;
       private java.sql.Timestamp createTime;
     }
     ```

   - 准备mapper接口

     ```Java
     public interface AirMapper {
         List<Message> findAll();
     }
     ```

   - 在启动类中，添加直接扫描Mapper接口所在的包

     ```Java
     @MapperScan(basePackages = "com.example.springboot01hello.mapper")
     ```

   - 准备映射文件

     ```xml
     <?xml version="1.0" encoding="utf-8" ?>
     <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     
     <mapper namespace="com.example.springboot01hello.mapper.AirMapper">
     <!--    List<Message> findAll();-->
         <select id="findAll" resultType="Air">
             select * from air
         </select>
     </mapper>
     ```

   - 添加yml文件配置信息

     ```yml
     #  mybatis配置
     mybatis:
     #  扫描映射文件
       mapper-locations: classpath:mapper/*.xml
     #  配置别名扫描的包
       type-aliases-package: com.example.springboot01hello.entity
       configuration:
     #    开启驼峰映射配置
         map-underscore-to-camel-case: true
     ```

   - 指定连接数据库的信息

     ```yml
     # 连接数据库的信息
     spring:
       datasource:
         driver-class-name: com.mysql.cj.jdbc.Driver
         url: jdbc:mysql:///satellite?serverTimezone=UTC
         username: root
         password: xxx1234567
         type: com.alibaba.druid.pool.DruidDataSource
     ```

3. 测试

   在Mapper接口的位置，直接右键 -> goto -> Test

   会自动创建当前接口的测试类，在Test目录下。

   让当前测试类，继承主程序测试类(在class前，添加public)

   ```Java
   class AirMapperTest extends SpringBoot01HelloApplicationTests {
   
       @Autowired
       private AirMapper airMapper;
   
       @Test
       void findAll() {
           List<Air> list = airMapper.findAll();
           for (Air air : list) {
               System.out.println(air);
           }
       }
   }
   ```

# 2. 注解方式整合mybatis

1. 创建District的Mapper接口

   ```Java
   public interface DistrictMapper {
       List<District> findAll(); 
   }
   ```

2. 添加mybatis注解

   针对增删改查：@insert ，@Delete，@Update，@Select

   还是需要在启动类中添加@MapperScan注解

   ```Java
   public interface DistrictMapper {
       @Select("select * from district")
       List<District> findAll();
   
       @Select("select * from district where id = #{id}")
       District findOneById(@Param("id") long id);
   }
   ```

3. 测试，看到执行的sql语句

   ```yml
   # 查看SQL语句的代码，在yml文件中：
   logging:
     level:
       com.example.springboot01hello.mapper: DEBUG
   ```

   ```Java
   class DistrictMapperTest extends SpringBoot01HelloApplicationTests {
       @Autowired
       private DistrictMapper districtMapper;
   
       @Test
       void findAll() {
           List<District> list = districtMapper.findAll();
           for (District district : list) {
               System.out.println(district);
           }
       }
   
       @Test
       void findOneById() {
           District one = districtMapper.findOneById(3);
           System.out.println(one);
       }
   }
   ```

# 3. SpringBoot整合分页助手

1. 导入依赖

   ```xml
   <dependency>
     <groupId>com.github.pagehelper</groupId>
     <artifactId>pagehelper-spring-boot-starter</artifactId>
     <version>1.2.10</version>
   </dependency>
   ```

2. 测试

   ```Java
   @Test
   public void findByPage() {
     //1.执行分页
     PageHelper.startPage(2,1);
     //2.执行查询
     List<District> all = districtMapper.findAll();
     //3.封装pageInfo对象
     PageInfo<District> PageInfo = new PageInfo<>(all);
     //4.输出
     for (District district : PageInfo.getList()) {
       System.out.println(district);
     }
   }
   ```

   
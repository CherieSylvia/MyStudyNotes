https://www.warlock.live/archives.html

# 1. 基本配置

1. 在IDEA创建一个简单的项目

2. 在pom.xml添加mybatis框架所需要的依赖的jar包

   ```xml
   <dependencies>
       <!--Mybatis只需要前两个-->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.4.5</version>
       </dependency>
   
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>5.1.6</version>
       </dependency>
   
       <dependency>
           <groupId>log4j</groupId>
           <artifactId>log4j</artifactId>
           <version>1.2.12</version>
       </dependency>
       
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.10</version>
       </dependency>
   </dependencies>
   ```

3. 完成主要配置文件的编写SqlMapConfig.xml（数据库连接和指定映射文件的位置[目的是为了不用自己实现数据库查询方法]）

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <!-- mybatis的主配置文件 -->
   <configuration>
     <!-- 配置环境 -->
     <environments default="mysql">
       <!-- 配置mysql的环境-->
       <environment id="mysql">
         <!-- 配置事务的类型-->
         <transactionManager type="JDBC"></transactionManager>
         <!-- 配置数据源（连接池） -->
         <dataSource type="POOLED">
           <!-- 配置连接数据库的4个基本信息 -->
           <property name="driver" value="com.mysql.jdbc.Driver"/>
           <property name="url" value="jdbc:mysql://localhost:3306/eesy?serverTimezone=UTC"/>
           <property name="username" value="root"/>
           <property name="password" value="xxx1234567"/>
         </dataSource>
       </environment>
     </environments>
   
     <!-- 指定映射配置文件的位置(映射配置文件指的是每个dao独立的配置文件) -->
     <mappers>
       <!--        基于注解开发(写接口的全限定类名)-->
       <mapper class="com.mybatis.dao.IUserDao"/>
       <!--        基于xml开发-->
       <!--                <mapper resource="com/mybatis/dao/IUserDao.xml"/>   &lt;!&ndash; 注意这里的文件结构最好和dao层结构相同(resources文件夹下) &ndash;&gt;-->
     </mappers>
   </configuration>
   ```
   
4.  创建实体类文件(domain.User)

5.  创建dao接口(dao.IUserDao/IUserMapper)

6. 基于xml开发的xml文件（resources文件夹下）

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace：名称空间：写接口的全类名，相当于告诉mybatis这个配置文件是实现哪个接口的-->
   <mapper namespace="com.mybatis.dao.IUserDao">
       <!--    id为接口的方法名称-->
       <select id="findAll" resultType="com.mybatis.domain.User">
           select * from user
       </select>
   </mapper>
   ```

7.  基于注解的操作

   修改SqlMapConfig.xml，并移除 xml 的映射配置（IUserDao.xml）。

   ```xml
   <mappers>
     <!--基于注解开发(写接口的全限定类名)-->
     <mapper class="com.mybatis.dao.IUserDao"/>
     <!--基于xml开发-->
     <!--<mapper resource="com/mybatis/dao/IUserDao.xml"/>   注意这里的文件结构最好和dao层结构相同(resources文件夹下) &ndash;&gt;-->
   </mappers>
   ```

8. 测试

   ```Java
   package com.mybatis.dao;
   
   class IUserDaoTest {
       public static void main(String[] args) throws Exception {
   //        测试基于注解的mybatis使用
           //1.读取配置文件
           InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
           //2.创建SqlSessionFactory工厂
           SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
           SqlSessionFactory factory = builder.build(in);
           //3.使用工厂生产SqlSession对象
           SqlSession session = factory.openSession();
           //4.使用SqlSession对象创建Dao接口的代理对象
           IUserDao userDao = session.getMapper(IUserDao.class);
           //5.使用代理对象执行方法
           List<User> users = userDao.findAll();
           for (User user : users) {
               System.out.println(user);
           }
           //6.释放资源
           session.close();
           in.close();
       }
   }
   ```

# 2. mybatis 对数据库的CRUD操作

```Java
@Select("select * from user")
List<User> findAll();

List<User> users = userDao.findAll();
for (User user : users) {
  System.out.println(user);
}
```

```Java
@Insert("insert into user(username,address,gender,birthday) values(#{username},#{address},#{gender},#{birthday})")
void insertUser(User user);

@Test
void insertTest() {
  User user = new User();
  user.setUsername("mybatis1");
  user.setAddress("西安");
  userDao.insertUser(user);
}
```

```Java
@Update("update user set username=#{username},address = #{address}, sex = #{sex}, birthday = #{birthday} where id = #{id}")
void updateUser(User user);

@Test
void updateTest() {
  User user = new User();
  user.setId(48);
  user.setUsername("xxx");
  user.setAddress("aaa");
  user.setSex("女");
  user.setBirthday(new Date());
  userDao.updateUser(user);
}
```

```Java
@Delete("delete from user where id=#{id} ")
void deleteUser(Integer userId);

@Test
void deleteTest() {
  userDao.deleteUser(48);
}
```

```Java
@Select("select * from user where usernmae like #{username}")    /*查询时写%mybatis%*/
@Select("select * from user where usernmae like '%${value}%'")   /*查询时直接写mybatis*/
void findUserByName(String username);

//    查询总用户数量
@Select("select count(*) from user")
int totalUser();
```

## 2.1 实体类属性名与数据库字段名不一致时

```Java
@Results(id="userMap",value={
  @Result(id=true,column = "id",property = "userId"),
  @Result(column = "username",property = "userName"),
  @Result(column = "address",property = "userAddress"),
  @Result(column = "sex",property = "userSex"),
  @Result(column = "birthday",property = "userBirthday"),
})

@ResultMap(value = {"userMap"})
```

> column为数据库字段名，property为实体类属性名
>
> id=true：代表这个字段是id字段。
>
> 写一次，在别的方法前添加注解 `@ResultMap(value = {"userMap"})`

## 2.2 单表查询：一对一单表查询

多对一的映射（mybatis中称之为一对一）：一个账户只能属于一个用户

1. 数据库外键展示：

   ![数据库外键展示](images/%E5%A4%96%E9%94%AE%E5%B1%95%E7%A4%BA.png)

2. 生成User实体类

3. 生成account实体类（在account中需要添加user表的关系映射）

   ![account](images/account%E5%AE%9E%E4%BD%93.png)

4. 根据注解编写account的SQL语句

   ```Java
   @Select("select * from account")
   @Results(id="accountMap",value = {
     @Result(id = true,column = "id",property = "id"),
     @Result(column = "uid",property = "uid"),
     @Result(column = "money",property = "money"),
     @Result(property = "user",column = "uid",one = @One(select = "com.example.mybatis.dao.IUserDao.findById",fetchType = FetchType.EAGER))
   })
   List<Account> findAll();
   ```

   > property指的依然是Account实体类的参数user
   >
   > column是根据UID来查询
   >
   > select找到可以实现功能的那个方法(通过id找user)
   >
   > 对一：选择EAGER    对多：选择LAZY

## 2.3 单表查询：一对多单表查询

一对多关系映射：一个用户对应多个账户

1. 数据库查询展示

   ![img](images/%E4%B8%80%E5%AF%B9%E5%A4%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9F%A5%E8%AF%A2%E5%B1%95%E7%A4%BA.png)

2. 在User实体类添加account表的关系映射

3. 给AccountDao.java添加通过uid的查询方法

   ```Java
   @Select("select * from account where uid = #{userId}")
   List<Account> findAccountByUid(Integer UserId);
   ```

4. 在UserDao.java用注解编写查询语句

   ```Java
   @Select("select * from user")
   @Results(id="userMap",value={
     @Result(id=true,column = "id",property = "userId"),
     @Result(column = "username",property = "userName"),
     @Result(column = "address",property = "userAddress"),
     @Result(column = "sex",property = "userSex"),
     @Result(column = "birthday",property = "userBirthday"),
     @Result(property = "account",column = "id",
             many = @Many(select = "com.example.mybatis.dao.IAccountDao.findAccountByUid",
                          fetchType = FetchType.LAZY))
   })
   List<User> findAll();
   ```

   
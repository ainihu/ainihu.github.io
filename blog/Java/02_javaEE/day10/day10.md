# day10

## 框架介绍

> - 框架：它是项目开发中的一套解决方案，不同的框架解决不同的问题。
> - 框架的好处：框架封装了很多的细节，使开发者可以使用极简的方式实现功能，大大提高开发效率。

## 三层架构

- 表现层：是用于展示数据；
- 业务层：是处理业务需求；
- 持久层：是与数据库交互。

## 持久层技术解决方案

> - `JDBC`技术：
>   - `Connection`
>   - `PrreparedStatement`
>   - `ResultSet`
> - `Spring`的`JdbcTemplate`
>   - `Spring`中对`jdbc`的简单封装；
> - `Apache`的`DBUtils`
>   - 与`Spring`的`JdbcTemplate`类似，也是对`jdbc`的简单封装；
>
> `JDBC`是规范，`Spring`的`JdbcTemplate`和`Apache`的`DBUtils`只是工具类，因此上面三种均不是框架

## Mybatis概述

> `Mybatis`是一个持久层框架，用`java`编写的，它封装了`jdbc`操作的很多细节，使开发者只需要关注`sql`语句本身，而无需关注注册驱动，创建连接等繁杂过程，它使用了ORM思想实现了结果集的封装。

### ORM

> `ORM`(`Object Relational Mapping`)对昂关系映射，把数据库表和实体类以及实体类的属性对应起来，让我们可以操作实体类就实现操作数据库表。

## Mybatis入门

### Mybatis的环境搭建

> 1. 创建Maven工程并导入坐标；
> 2. 创建实体类和dao的接口；
> 3. 创建Mybatis的主配置文件`SqlMapConfig.xml`；
> 4. 创建映射配置文件`UserDao.xml`。

### 环境搭建注意事项

> 1. 创建`UserDao.xml`和`UserDao.java`时名称为了和之前的知识保持一致，所以在`Mybatis`中把持久层的操作接口名称和映射文件也叫做`Mapper`，因此，`UserDao`和`UserMapper`表达的意思一样。
> 2. 在`idea`创建目录的时候，它和包不同。
>    - 包创建`com.jianmo.dao`，它是**三级目录结构**；
>    - 目录创建时`com.jianmo.dao`，是**一级目录结构**。
> 3. `Mybatis`的映射配置文件必须和`dao`接口的包结构相同。
> 4. 映射配置文件的`Mapper`标签`namespace`属性取值必须是`dao`接口的全限定类名。
> 5. 映射配置文件的操作配置，`id`属性的取值，必须是`dao`接口的方法名。
>
> 当遵守`3、4、5`点后，我们以后开发中不需要再写`dao`的实现类。
>
> 注意事项：
>
> - `xml`配置时，不要忘记在映射配置中告诉`Mybatis`要封装到哪个实体类汇总，配置的方式：指定实体类的全限定类名

### Mybatis使用步骤

> 1. 读取配置文件；
> 2. 创建`SqlSessionFactory`工厂对象；
> 3. 使用工厂生成`SqlSession`对象；
> 4. 使用`SqlSession`创建`Dao`代理对象；
> 5. 使用代理对象执行方法；
> 6. 释放资源；

```java
package com.jianmo.test;

import com.jianmo.dao.UserDao;
import com.jianmo.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * Mybatis入门测试案例
 */

public class MybatisTest {

	/**
	 * 入门案例
	 * @param args
	 */
	public static void main(String[] args) throws IOException {
		// 1.读取配置文件
		final InputStream resource = Resources.getResourceAsStream("SqlMapConfig.xml");

		// 2.创建SqlSessionFactory工厂
		final SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();  // 构建者模式
		final SqlSessionFactory factory = builder.build(resource);  // 工厂模式

		// 3.使用工厂生成SqlSession对象
		final SqlSession session = factory.openSession();

		// 4.使用SqlSession创建Dao代理对象
		final UserDao userDao = session.getMapper(UserDao.class);  // 动态代理模式

		// 5.使用代理对象执行方法
		final List<User> all = userDao.findAll();
		for (User user : all) {
			System.out.println(user);
		}

		// 6.释放资源
		session.close();
		resource.close();
	}
}
```

### Mybatis注解

> 使用步骤(基于`xml`配置)：
>
> 把`UserDao.xml`移除，在`dao`接口的方法上使用`@Select`注解，并且指定SQL语句同时需要在`SqlMapConfig.xml`中的`mapper`配置时，使用`class`属性指定`dao`接口的全限定类名。

## Mybatis实现CRUD操作

> `Mybatis`的`CRUD`、模糊查询、查询一行一列、获取插入数据的`id`、包装(`pojo`)对象查询。

### 注解方式

```java
package com.jianmo.dao;

import com.jianmo.domain.QueryVo;
import com.jianmo.domain.User;
import org.apache.ibatis.annotations.*;

import javax.management.Query;
import java.util.List;

/**
 * 用户持久层接口
 */

public interface UserDao {
	/**
	 * 查询所有数据
	 * @return
	 */
	@Select("select * from user;")
	List<User> findAll();

	/**
	 * 保存用户信息
	 * @param user
	 */
	@SelectKey(statement = "select last_insert_id();",keyProperty = "id", keyColumn = "id", before = false, resultType = Integer.class)
	@Insert("insert into user(username, address, sex, birthday) values(#{username}, #{address}, #{sex}, #{birthday})")
	void saveUser(User user);

	/**
	 * 更新用户信息
	 * @param user
	 */
	@Update("update user set username=#{username}, address=#{address}, sex=#{sex}, birthday=#{birthday} where id=#{id}")
	void updateUser(User user);

	/**
	 * 删除用户信息
	 * @param userID
	 */
	@Delete("delete from user where id=#{userID}")
	void deleteUser(Integer userID);


	/**
	 * 根据id查询用户
	 * @param userID
	 * @return
	 */
	@Select("select * from user where id=#{userID}")
	User findByID(Integer userID);

	/**
	 * 通过用户名模糊查询用户，参数需要提供%
	 * @param name
	 * @return
	 */
	@Select("select * from user where username like #{name}")  // 方式一模糊查询，sql预编译，参数占位符(优)
	// @Select("select * from user where username like '%${value}%'")  // 方式二模糊查询，字符串拼接，必须写成value
	List<User> findByName(String name);

	/**
	 * 查询总记录数
	 * @return
	 */
	@Select("select count(id) from user")
	Integer findTotal();

	/**
	 * 根据QueryVo查询中的条件查询用户
	 *
	 * @param vo
	 * @return
	 */
	@Select({"select * from user where username  like #{user.username}"})
	List<User> findUserByVo(QueryVo vo);
}
```

### xml方式

> `resultMap`子元素`id`代表`resultMap`的主键，而`result`代表其属性。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jianmo.dao.UserDao">

    <!-- 配置 查询结果的列名和实体类的属性名的对应关系 -->
    <resultMap id="userMap" type="User">
        <!-- 主键字段的对应 -->
        <id property="userId" column="id"></id>
        <!--非主键字段的对应-->
        <result property="userName" column="username"></result>
        <result property="userAddress" column="address"></result>
        <result property="userSex" column="sex"></result>
        <result property="userBirthday" column="birthday"></result>
    </resultMap>


    <!-- 查询所有 -->
    <select id="findAll" resultMap="userMap">
        <!--select id as userId,username as userName,address as userAddress,sex as userSex,birthday as userBirthday from user;-->
        select * from user;
    </select>

    <!-- 保存用户 -->
    <insert id="saveUser" parameterType="user">
        <!-- 配置插入操作后，获取插入数据的id -->
        <selectKey keyProperty="userId" keyColumn="id" resultType="int" order="AFTER">
            select last_insert_id();
        </selectKey>
        insert into user(username,address,sex,birthday)values(#{userName},#{userAddress},#{userSex},#{userBirthday});
    </insert>

    <!-- 更新用户 -->
    <update id="updateUser" parameterType="USER">
        update user set username=#{userName},address=#{userAddress},sex=#{userAex},birthday=#{userBirthday} where id=#{userId}
    </update>

    <!-- 删除用户-->
    <delete id="deleteUser" parameterType="java.lang.Integer">
        delete from user where id = #{uid}
    </delete>
    
    <!-- 根据id查询用户 -->
    <select id="findById" parameterType="INT" resultMap="userMap">
        select * from user where id = #{uid}
    </select>

    <!-- 根据名称模糊查询 -->
    <select id="findByName" parameterType="string" resultMap="userMap">
          select * from user where username like #{name}
        <!-- select * from user where username like '%${value}%'-->
   </select>

    <!-- 获取用户的总记录条数 -->
    <select id="findTotal" resultType="int">
        select count(id) from user;
    </select>

    <!-- 根据queryVo的条件查询用户 -->
    <select id="findUserByVo" parameterType="com.jianmo.domain.QueryVo" resultMap="userMap">
        select * from user where username like #{user.username}
    </select>
</mapper>
```

## ResultMap中的id和result的区别

> 参考链接：https://www.cnblogs.com/1iHu4D0n9/p/9059594.html
>
> 子元素`id`代表`resultMap`的**主键**，而`result`代表其**属性**。
>
> 在自定义的`resultMap`中第一列通常是主键`id`，那么`id `和`result`有什么区别呢？
>
> - `id`和`result`都是映射单列值到一个属性或字段的简单数据类型。
> - 唯一不同的是，`id`是作为唯一标识的，当和其他对象实例对比的时候，这个`id`很有用，尤其是应用到缓存和内嵌的结果映射。

## Mybatis的参数深入类型

### OGNL表达式

> `OGNL`(`Object Graphic Navigation Language`)对象图导航语言，是通过对象中的方法来获取数据，在写法上把`get`给省略了。
>
> 获取用户名称：
>
> - 类中的写法：`user.getUsername();`
> - `OGNL`表达式写法：`user.username();`
>
> `Mybatis`中可以直接写`username`不指定对象名，是因为`parameterType`中已经提供了属性所属的类。

## SqlMapConfig配置文件

### properties

> 可以在`xml`文件的`configuration`标签中定义`properties`子标签。
>
> - 通过`properties`标签的`resource`属性：引入配置文件的位置，是按照类路径的写法来写，并且必须存在于类路径下。
> - 通过`properties`标签的`url`属性：要求按照`url`的写法来写地址。
>   - `url`：统一资源定位符；
>   - `uri`：统一资源标识符。
>
> ```xml
> <configuration>
> 	<properties url="file:///D:/IdeaProjects/day02_eesy_01mybatisCRUD/src/main/resources/jdbcConfig.properties">
>        <!-- <property name="driver" value="com.mysql.jdbc.Driver"></property>
>         <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"></property>
>         <property name="username" value="root"></property>
>         <property name="password" value="1234"></property>-->
> 	</properties>
> </configuration>
> ```
>
> 

### typeAliases

> 可以在`xml`文件的`configuration`标签中定义`typeAliases`子标签。
>
> - 通过`typeAlias`标签的属性`type`是实体类全限定类名，`alias`属性指定别名，当制定了别名就**不再区分大小写**；
> - 通过`package`标签的属性`name`配置包的别名，当指定后该包下的所有**实体类**都会注册别名，并且类名就是别名**不再区分大小写**。
>
> ```xml
> <configuration>
> 	<typeAliases>
>         <!-- typeAlias用于配置别名。type属性指定的是实体类全限定类名。alias属性指定别名，当指定了别名就再区分大小写 -->
>         <typeAlias type="com.itheima.domain.User" alias="user"></typeAlias>
> 
>         <!-- 用于指定要配置别名的包，当指定之后，该包下的实体类都会注册别名，并且类名就是别名，不再区分大小写-->
>         <package name="com.itheima.domain"></package>
>     </typeAliases>
> </configuration>
> ```

### mappers

> `mappers`标签可以通过配置`package`子标签适用于指定`dao`的**接口**所在的包，当指定接口后就不需要写`mapper`以及`resource`或者`class`。

## Mybatis连接池

> `Mybatis`连接池的三种配置`POOLED`、`UNPOOLED`和`JNDI`：
>
> - 主配置文件`SqlMapConfig.xml`中的`dataSource`标签，`type`属性表示采用何种连接池的方式；
>   - `type`属性的取值：
>     - `POOLED`：采用传统的`javax.sql.DataSource`规范中的连接池，`Mybatis`中有针对规范的实现；
>     - `UNPOOLED`：采用传统的获取连接的方式，虽然也实现了`javax.sql.DataSource`接口，但是没有使用连接池的思想；
>     - `JNDI`：采用服务器提供的`JNDI`技术实现，来获取`DataSource`对象，不同的服务器所能拿到的`DataSource`不同。**注意**：如果不是`web`或者`Maven`的`war`工程，则不能使用。如果使用`tomcat`服务器，采用连接池就是`dbcp`连接池。

## Mybatis事务控制

> - `commit`
> - `rollback`

## Mybatis基于XML配置的SQL语句使用

> `mappers`配置文件中的几个标签；
>
> - `<if>`
>
>   > ```xml
>   > <!-- 根据参数条件查询:if标签-->
>   > <select id="findUserByCondition" parameterType="com.jianmo.domain.User" resultType="com.jianmo.domain.User">
>   >     select * from user where 1=1
>   >     <if test="username != null">
>   >         and username=#{username}
>   >     </if>
>   >     <if test="sex != null">
>   >         and sex=#{sex}
>   >     </if>
>   > </select>
>   > ```
>
> - `<where>`，避免加`where 1=1`条件
>
>   > ```xml
>   > <!-- 根据参数条件查询:where标签-->
>   > <select id="findUserByCondition" parameterType="com.jianmo.domain.User" resultType="com.jianmo.domain.User">
>   >     select * from user
>   >     <where>
>   >         <if test="username != null">
>   >             and username=#{username}
>   >         </if>
>   >         <if test="sex != null">
>   >             and sex=#{sex}
>   >         </if>
>   >     </where>
>   > </select>
>   > ```
>
> - `<foreach>`
>
>   > ```xml
>   > <!--根据QueryVo的ids集合查询用户信息列表-->
>   > <select id="findUserByIds" parameterType="com.jianmo.domain.QueryVo" resultType="com.jianmo.domain.User">
>   >     select * from user
>   >     <where>
>   >         <if test="ids != null and ids.size() > 0">
>   >             <foreach collection="ids" open="and id in(" close=")" item="id" separator=",">
>   >                 #{id}  <!-- item="id" -->
>   >             </foreach>
>   >         </if>
>   >     </where>
>   > </select>
>   > ```
>
> - `<sql>`，抽取重复的`sql`代码，且只需要修改一次。
>
>   > ```xml
>   > <!-- 定义sql标签进行去除重复代码 -->
>   > <sql id="defaultUserSQL">
>   >     select * from user
>   > </sql>
>   > 
>   > <!-- 根据参数条件查询:where标签，并引用sql语句-->
>   > <select id="findUserByCondition" parameterType="com.jianmo.domain.User" resultType="com.jianmo.domain.User">
>   >     <!-- select * from user -->
>   >     <include refid="defaultUserSQL"></include>  <!-- 引用sql标签 -->
>   > 
>   >     <where>
>   >         <if test="username != null">
>   >             and username=#{username}
>   >         </if>
>   >         <if test="sex != null">
>   >             and sex=#{sex}
>   >         </if>
>   >     </where>
>   > </select>
>   > ```

## Mybatis的多表操作

### 一对多和多对一

> ```xml
> <mapper namespace="com.jianmo.dao.AccountDao">
> 
>     <!-- 查询所有 -->
>     <select id="findAll" resultType="com.jianmo.domain.Account">
>         select * from account;
>     </select>
>     <!-- 查询所有账户信息并带用户名和地址信息：一对多 -->
>     <select id="findAllAccount" resultType="com.jianmo.domain.AccountUser">
>         select account.*, user.address, user.username from user, account where user.id = account.uid;
>     </select>
> </mapper>
> ```

### 一对一

> ```xml
> <mapper namespace="com.jianmo.dao.UserDao">
> 
>     <!-- 查询所有 -->
>     <select id="findAll" resultType="com.jianmo.domain.User">
>         select * from user;
>     </select>
> 
>     <!-- 根据id查询用户：一对一 -->
>     <select id="findByID" parameterType="INT" resultType="com.jianmo.domain.User">
>         select * from user where id = #{uid}
>     </select>
> 
> </mapper>
> ```

### 多对多

> ```xml
> <mapper namespace="com.jianmo.dao.RoleDao">
> 
>     <resultMap id="roleMap" type="com.jianmo.domain.Role">
>         <id property="roleId" column="id"></id>
>         <result property="roleName" column="role_name"></result>
>         <result property="roleDesc" column="role_desc"></result>
>         <collection property="users" ofType="com.jianmo.domain.User">
>             <id column="id" property="id"></id>
>             <result column="username" property="username"></result>
>             <result column="birthday" property="birthday"></result>
>             <result column="sex" property="sex"></result>
>             <result column="address" property="address"></result>
>         </collection>
>     </resultMap>
> 
>     <resultMap id="userMap" type="com.jianmo.domain.User">
>         <id column="id" property="id"></id>
>         <result column="username" property="username"></result>
>         <result column="birthday" property="birthday"></result>
>         <result column="sex" property="sex"></result>
>         <result column="address" property="address"></result>
>         <collection property="roles" ofType="com.jianmo.domain.Role">
>             <id property="roleId" column="rid"></id>
>             <result property="roleName" column="ROLE_NAME"></result>
>             <result property="roleDesc" column="ROLE_DESC"></result>
>         </collection>
>     </resultMap>
> 
>     <!--查询一个角色对应的多个用户：多对多-->
>     <select id="findAllRole" resultMap="roleMap">
>         select user.*, role.ID as rid, role.ROLE_NAME, role.ROLE_DESC from role
>             left outer join user_role on role.ID = user_role.RID
>             left outer join user on user_role.UID = user.id
>     </select>
> 
>     <!--查询一个用户对应的多个角色：多对多-->
>     <select id="findAllUser" resultMap="userMap">
>         select user.*, role.id as rid, role.ROLE_DESC, role.ROLE_NAME from user
>         left outer join user_role on user.id = user_role.UID
>         left outer join role on user_role.RID = role.ID;
>     </select>
> 
> </mapper>
> ```

## JDNI

> 


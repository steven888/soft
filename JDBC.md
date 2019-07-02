# JDBC

[TOC]

---

## 1 JDBC概述

---

### 1.1 持久化(persistence)

- **把数据保存到可掉电式存储设备中以供之后使用**。大多数情况下，特别是企业级应用，**数据持久化意味着将内存中的数据保存到硬盘上加以”固化”**，而持久化的实现过程大多通过各种`关系数据库`来完成。

- 持久化的主要应用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML数据文件中。 

---

### 1.2 Java中的数据存储技术

- 在 **Java** 中，数据库存取技术可分为如下几类：
  - `JDBC`直接访问数据库
  - `JDBC`间接访问数据库: `JDO`技术、第三方O/R工具，如`Hibernate`, `Mybatis`等
- `JDBC`是`java`访问数据库的基石，`JDO`, Hibernate等只是更好的封装了`JDBC`

---

### 1.3 JDBC基础

- **JDBC(Java Database Connectivity)**是一个**独立于特定数据库管理系统、通用的SQL数据库存取和操作的公共接口**（一组API），定义了用来访问数据库的标准Java类库，使用这个类库可以以一种标准的方法、方便地访问数据库资源
- JDBC为访问不同的数据库提供了一种**统一的途径**，为开发者屏蔽了一些细节问题
- JDBC的目标是使Java程序员使用JDBC可以连接任何提供了JDBC驱动程序的数据库系统，这样就使得程序员无需对特定的数据库系统的特点有过多的了解，从而大大简化和加快了开发过程

---

#### 1.3.1 如果没有JDBC会带来哪些问题？

- 访问数据库没有规范
- 因为数据库厂商是不一样的,就会执行自己访问数据库的规则

---

### 1.4 JDBC体系结构

- **JDBC**接口（API）包括两个层次：
  - **面向应用的API**：Java API，抽象接口，供应用程序开发人员使用（连接数据库，执行SQL语句，获得结果）
  - **面向数据库的API**：Java Driver API，供开发商开发数据库驱动程序用

---

### 1.5 JDBC驱动程序分类

- **JDBC**驱动程序：各个数据库厂商根据**JDBC**的规范制作的**JDBC**实现类的类库
- **JDBC**驱动程序总共有四种类型：

  - 第一类：JDBC-ODBC桥
  - 第二类：部分本地API部分Java的驱动程序
  - 第三类：JDBC网络纯Java驱动程序
  - 第四类：**本地协议的纯 Java 驱动程序**
- 第三、四两类都是纯Java的驱动程序，因此，对于Java开发者来说，它们在性能、可移植性、功能等方面都有优势

---

#### 1.5.1 ODBC

- 早期对数据库的访问，都是调用数据库厂商提供的专有的 API。为了在 ==Windows 平台==下提供统一的访问方式，微软推出了 **ODBC(Open Database Connectivity**，开放式数据库连接)，并提供了 ODBC API，使用者在程序中只需要调用 ODBC API，由 ODBC 驱动程序将调用转换成为对特定的数据库的调用请求
- 一个基于ODBC的应用程序对数据库的操作不依赖任何DBMS(database manager system)，不直接与DBMS打交道，所有的数据库操作由对应的DBMS的==ODBC驱动程序==完成。也就是说，不论是FoxPro、Access , MYSQL还是Oracle数据库，均可用ODBC API进行访问。由此可见，ODBC的最大优点是能以统一的方式处理所有的数据库

---

#### 1.5.2 JDBC-ODBC桥

- JDBC-ODBC 桥本身也是一个驱动，利用这个驱动，可以使用 JDBC-API 通过ODBC 去访问数据库。这种机制实际上是把标准的 JDBC 调用转换成相应的 ODBC 调用，并通过 ODBC 访问数据库
- 因为需要通过多层调用，所以利用 JDBC-ODBC 桥访问数据库的效率较低
- 在 JDK 中，提供了 JDBC-ODBC 桥的实现类(sun.jdbc.odbc.JdbcOdbcDriver)

---

#### 1.5.3 部分本地API部分Java的驱动程序

- 这种类型的 JDBC 驱动程序使用 Java 编写，它调用数据库厂商提供的本地 API
- 通过这种类型的 JDBC 驱动程序访问数据库减少了 ODBC 的调用环节，提高了数据库访问的效率
- 在这种方式下需要在客户的机器上安装本地 JDBC 驱动程序和特定厂商的本地 API 

---

#### 1.5.4 JDBC网络纯Java驱动程序

- 这种驱动利用中间件的应用服务器来访问数据库。应用服务器作为一个到多个数据库的网关，客户端通过它可以连接到不同的数据库服务器
- 应用服务器通常有自己的网络协议，Java 用户程序通过 JDBC 驱动程序将 JDBC 调用发送给应用服务器，应用服务器使用本地程序驱动访问数据库，从而完成请求

---

#### 1.5.5 本地协议的纯 Java 驱动程序

##### 1.5.5.1 JDBC API

- **JDBC API** 是一系列的接口，它使得应用程序能够进行数据库联接，执行SQL语句，并且得到返回结果。

---

##### 1.5.5.2 Driver 接口

- **Java.sql.Driver** 接口是所有 **JDBC** 驱动程序需要实现的接口。这个接口是提供给数据库厂商使用的，不同数据库厂商提供不同的实现
- 在程序中不需要直接去访问实现了 **Driver** 接口的类，而是由驱动程序管理器类(**java.sql.DriverManager**)去调用这些**Driver**实现

---

##### 1.5.5.3 建立连接

- 可以调用 **DriverManager** 类的 **getConnection()** 方法建立到数据库的连接
- **JDBC URL** 用于标识一个被注册的驱动程序，驱动程序管理器通过这个 **URL** 选择正确的驱动程序，从而建立到数据库的连接
- **JDBC URL** 的标准由三部分组成，各部分间用冒号分隔
  - jdbc:<子协议>:<子名称>
  - 协议：JDBC URL中的协议总是jdbc
  - 子协议：子协议用于标识一个数据库驱动程序
  - 子名称：一种标识数据库的方法。子名称可以依不同的子协议而变化，用子名称的目的是为了定位数据库提供足够的信息

---

##### 1.5.5.4 几种常用数据库的JDBC URL

- 对于 Oracle 数据库连接，采用如下形式： 
  - ==jdbc:oracle:thin:@localhost:1521:sid==
- 对于 SQLServer 数据库连接，采用如下形式：
  - ==jdbc:microsoft:sqlserver//localhost:1433; DatabaseName=sid==
- 对于 MYSQL 数据库连接，采用如下形式：
  - ==jdbc:mysql://localhost:3306/sid==

---

#### 1.5.6 常见问题和错误

==忘记引入驱动包==

==在 JAVA 代码中使用 import 引入的时候本应该引入 java.sql.xxx 接口却引入了com.mysql.xxx类==

==`JAVA6`开始`JDBC4.0`(有一个新特性：无需加载注册驱动)==

==`JAVA7`开始`JDBC4.1`==

---

#### 1.5.7 中文乱码处理

查看 MySQL字符集

```mysql
show variables like '%char%';  
```

- `C:\ProgramData\MySQL\MySQL Server 5.7\my.ini`
- 修改`[mysqld]` `character-set-server=utf8`
  - ![Markdown](https://user-images.githubusercontent.com/4152130/60423983-ec128000-9c21-11e9-9b2a-a0e0b255dca2.png)

`services.msc`重启mysql服务

---

## 2 JDBC完成CRUD

### 2.1 获取数据库连接

#### 2.1.1 加载注册驱动

```java
Class.forName("com.mysql.jdbc.Driver");
```

==为什么上述这一行代码就能完成加载和注册驱动操作呢？==

- `Class.forName com.mysql.jdbc.Driver`会把这份字节码加载进`JVM`
- 当一份字节码被加载进`JVM`时，就会执行该字节码中的静态代码块
- **静态代码块启动后通过`java.sql.DriverManager.registerDriver(new Driver());`注册驱动**

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    //
    // Register ourselves with the DriverManager
    //
    static {
        try {
            java.sql.DriverManager.registerDriver(new Driver());
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
```

---

#### 2.1.2 获取连接

- 通过`Driver`类的`getConnection`方法来获取连接对象

  - ```java
    	/**
      * Driver 是一个接口:数据库厂商提供实现接口，能从其中获取数据库连接.
      * 代码耦合即有一个Driver要new一个对饮的Driver
      * @throws Exception
     */
    @Test
    public void testDriver() throws Exception {
      	// 1、创建一个Driver 实现类的对象
      	Driver driver = new com.mysql.jdbc.Driver();
      
      	// 2、连接数据库的基本信息：url，user，password
      	String url = "jdbc:mysql://localhost:3306/clouddb01";
      	Properties info = new Properties();
      	info.put("user", "root");
      	info.put("password", "steven123");
    
      	// 3、调用Driver接口的connect(url, info)获取数据库连接
      	Connection connection = driver.connect(url, info);
      	System.out.println(connection);
        TimeUnit.SECONDS.sleep(5);//睡眠5秒查看数据库连接信息
    }
    ```
    
  - ```java
    	/**
      	 * 编写一种通用的方法，在不修改原程序的情况下，可以获取任意数据库的连接
      	 * 解决方案:把数据库驱动 Driver 实现类的全类名，url,user,password 放入一个
      	 * 配置文件中，通过修改配置文件的方式实现和具体的数据库解耦。
      	 * 
      	 * @return
      	 * @throws Exception
      	 */
    public Connection getConnection() throws Exception {
      	String driverClass = null;
      	String url = "";
      	String user = null;
      	String passWord = null;
      	// 读取类路径下的jdbc.properties文件
      	File file = ResourceUtils.getFile("classpath:jdbc.properties");
      	FileInputStream is = new FileInputStream(file);
      
      	Properties properties = new Properties();
      	properties.load(is);
      
      	driverClass = properties.getProperty("driver");
      	url = properties.getProperty("url");
      	user = properties.getProperty("user");
      	passWord = properties.getProperty("passWord");
      
      	Driver driver = (Driver) Class.forName(driverClass).newInstance();
      	Properties info = new Properties();
      	info.put("user", user);
      	info.put("password", passWord);
      	Connection connection = driver.connect(url, info);
        TimeUnit.SECONDS.sleep(5);//睡眠5秒查看数据库连接信息
      	return connection;
    }
    ```
    
  - ```properties
     driver = com.mysql.jdbc.Driver
     url = jdbc:mysql://localhost:3306/clouddb01
     user = root
     passWord = steven123
     ```
  
- 通过`DriverManager`类的`getConnection`方法来获取连接对象

```java
@Test
public void testDriverManager() throws Exception {
	String driverClass = null;
	String url = "";
	String user = null;
	String passWord = null;
	// 读取类路径下的jdbc.properties文件
	File file = ResourceUtils.getFile("classpath:jdbc.properties");
	FileInputStream is = new FileInputStream(file);

	Properties properties = new Properties();
	properties.load(is);
	driverClass = properties.getProperty("driver");
	url = properties.getProperty("url");
	user = properties.getProperty("user");
	passWord = properties.getProperty("passWord");
	//注冊驱动 从1.6开始可以不用注册
	Class.forName(driverClass);
	Connection connection = DriverManager.getConnection(url, user, passWord);
	System.out.println(connection);
    TimeUnit.SECONDS.sleep(5);//睡眠5秒查看数据库连接信息
}
```

```properties
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/clouddb01
user = root
passWord = steven123
```

---

#### 2.1.3 如何检测我们获取了数据库连接

```sql
show processlist
```

==`Java`1.6开始可以读取驱动包下面META-INF下面service下的`java.sql.Driver`==

![Markdown](https://user-images.githubusercontent.com/4152130/60424235-7824a780-9c22-11e9-8ca0-62ea9d11e185.png)

==注意：必须手动加载注册驱动==

---

### 2.2 DDL和DML操作

#### 2.2.1 操作JDBC步骤

- 加载注册驱动
- 获取连接对象
- 创建语句对象
- 执行SQL语句
- 释放资源

---

#### 2.2.2 创建表DDL

```sql
CREATE TABLE `t_student` (
  `id` bigint(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

- `int executeUpdate(String sql) `
  - 如果执行`DML`返回是受影响的行数
  - 如果执行`DDL`返回0

```java
@Test
public void testDDL() throws Exception {
	String sql = "CREATE TABLE `t_student` ( `id` int(11) NOT NULL AUTO_INCREMENT, `name` varchar(255) DEFAULT NULL, `age` int(11) DEFAULT NULL, PRIMARY KEY (`id`) ) ENGINE = InnoDB CHARSET = utf8;";
	// 加载注册驱动
	Class.forName("com.mysql.jdbc.Driver");
	String url = "jdbc:mysql://localhost:3306/clouddb01";
	String user = "root";
	String passWord = "steven123";
	// 获数据库连接对象
	Connection connection = DriverManager.getConnection(url, user, passWord);
	// 创建语句对象
	Statement statement = connection.createStatement();
	// 执行SQL语句
	long result = statement.executeLargeUpdate(sql);
	System.out.println(result);
    //释放资源
	statement.close();
	connection.close();
}
```

---

#### 2.2.3 异常处理

```java
@Test
public void testException() {
	Statement statement = null;
	Connection connection = null;
	String sql = "CREATE TABLE `t_student` ( `id` int(11) NOT NULL AUTO_INCREMENT, `name` varchar(255) DEFAULT NULL, `age` int(11) DEFAULT NULL, PRIMARY KEY (`id`) ) ENGINE = InnoDB CHARSET = utf8;";
	String url = "jdbc:mysql://localhost:3306/clouddb01";
	String user = "root";
	String passWord = "steven123";
	try {
		// 加载注册驱动
		Class.forName("com.mysql.jdbc.Driver");
		// 获数据库连接对象
		connection = DriverManager.getConnection(url, user, passWord);
		// 创建语句对象
		statement = connection.createStatement();
		// 执行SQL语句
		long result = statement.executeLargeUpdate(sql);
		System.out.println(result);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (null != statement) {
			try {
				statement.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

		if (null != connection) {
			try {
				connection.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

	}
}
```

---

#### 2.2.4 增删改DML

- 新增一个用户

```java
@Test
public void testInsert() throws Exception {
	String sql = "INSERT INTO `clouddb01`.`t_student` ( `name`, `age`) VALUES ( 'steven', '11');";
	// 加载注册驱动
	Class.forName("com.mysql.jdbc.Driver");
	String url = "jdbc:mysql://localhost:3306/clouddb01";
	String user = "root";
	String passWord = "steven123";
	// 获数据库连接对象
	Connection connection = DriverManager.getConnection(url, user, passWord);
	// 创建语句对象
	Statement statement = connection.createStatement();
	// 执行SQL语句
	long result = statement.executeLargeUpdate(sql);
	System.out.println(result);
	statement.close();
	connection.close();
}
```



- 修改一个用户

```java
@Test
public void testUpdate() throws Exception {
	String sql = "update t_student set `name` = 'king' where id =2";
	// 加载注册驱动
	Class.forName("com.mysql.jdbc.Driver");
	String url = "jdbc:mysql://localhost:3306/clouddb01";
	String user = "root";
	String passWord = "steven123";
	// 获数据库连接对象
	Connection connection = DriverManager.getConnection(url, user, passWord);
	// 创建语句对象
	Statement statement = connection.createStatement();
	// 执行SQL语句
	long result = statement.executeLargeUpdate(sql);
	System.out.println(result);
	statement.close();
	connection.close();
}
```

- 删除一个用户

```java
@Test
public void testDelete() throws Exception {
	String sql = "delete from t_student  where id =2 ";
	// 加载注册驱动
	Class.forName("com.mysql.jdbc.Driver");
	String url = "jdbc:mysql://localhost:3306/clouddb01";
	String user = "root";
	String passWord = "steven123";
	// 获数据库连接对象
	Connection connection = DriverManager.getConnection(url, user, passWord);
	// 创建语句对象
	Statement statement = connection.createStatement();
	// 执行SQL语句
	long result = statement.executeLargeUpdate(sql);
	System.out.println(result);
	statement.close();
	connection.close();
}
```

---

### 2.3 DQL操作

- `ResultSet executeQuery(String sql)`
  - 执行`DQL`语句，返回的数据就是结果集对象
- `ResultSet`接口：执行DQL操作后，会产生一个结果集 ，而`ResultSet`就是对结果集做的封装
- boolean next()
  - 先判断光标是否能往下移动一行，如果可以移动返回`true`,不可移动返回`false`
-  `XXX getXXXl(int columnIndex) `
  - 获取光标当前所在的行，第`columnIndex`列的数据并返回 ==下标从1开始==
  - `XXX`表示数据的类型，比如：`String`、`int`、`long`、`date`等

- `XXX getXXX(String columnLabel) `
  - 获取光标当前所在的行，指定==列名(可以是别名)==的值
- void close()
  - 释放资源

==`在JAVA中只有JDBC和JPA序号是从1开始的`==

---

#### 2.3.1 查询单行数据

- 返回`count`

  - ```java
    @Test
    public void testCount() throws Exception {
    	String sql = "select count(1) from t_student";
    	// 加载注册驱动
    	Class.forName("com.mysql.jdbc.Driver");
    	String url = "jdbc:mysql://localhost:3306/clouddb01";
    	String user = "root";
    	String passWord = "steven123";
    	// 获数据库连接对象
    	Connection connection = DriverManager.getConnection(url, user, passWord);
    	// 创建语句对象
    	Statement statement = connection.createStatement();
    	// 执行SQL语句
    	ResultSet resultSet = statement.executeQuery(sql);
    	if (resultSet.next())
    		System.out.println(resultSet.getLong(1));
    	resultSet.close();
    	statement.close();
    	connection.close();
    }
    ```

- 查询id为1的用户

  - ```java
    @Test
    public void testQueryId() throws Exception {
    	String sql = "select * from t_student where id = 1";
    	// 加载注册驱动
    	Class.forName("com.mysql.jdbc.Driver");
    	String url = "jdbc:mysql://localhost:3306/clouddb01";
    	String user = "root";
    	String passWord = "steven123";
    	// 获数据库连接对象
    	Connection connection = DriverManager.getConnection(url, user, passWord);
    	// 创建语句对象
    	Statement statement = connection.createStatement();
    	// 执行SQL语句
    	ResultSet resultSet = statement.executeQuery(sql);
    	if (resultSet.next()) {
    		System.out.println(resultSet.getLong(1));
    		System.out.println(resultSet.getString(2));
    		System.out.println(resultSet.getInt(3));
    	}
    	resultSet.close();
    	statement.close();
    	connection.close();
    }
    ```

---

#### 2.3.2 查询多行数据

- 查询全部用户

  - ```java
    @Test
    public void testQuery() throws Exception {
    	String sql = "select * from t_student";
    	// 加载注册驱动
    	Class.forName("com.mysql.jdbc.Driver");
    	String url = "jdbc:mysql://localhost:3306/clouddb01";
    	String user = "root";
    	String passWord = "steven123";
    	// 获数据库连接对象
    	Connection connection = DriverManager.getConnection(url, user, passWord);
    	// 创建语句对象
    	Statement statement = connection.createStatement();
    	// 执行SQL语句
    	ResultSet resultSet = statement.executeQuery(sql);
    	while (resultSet.next()) {
    		System.out.print(resultSet.getLong(1) + "\t");
    		System.out.print(resultSet.getString(2) + "\t");
    		System.out.println(resultSet.getInt(3));
    	}
    	resultSet.close();
    	statement.close();
    	connection.close();
    }
    ```

---

#### 2.3.3 SQL和JAVA的对应类型

|  类型名称   |        JAVA类型        |
| :---------: | :--------------------: |
|  `varchar`  |   `java.lang.String`   |
|   `char`    |   `java.lang.String`   |
|   `blob`    |   `java.lang.byte[]`   |
|    text     |   `java.lang.String`   |
|   integer   |  `java.lang.integer`   |
|  `tinyint`  |  `java.lang.integer`   |
| `smallint`  |  `java.lang.integer`   |
| `mediumint` |  `java.lang.integer`   |
|    `bit`    |  `java.lang.boolean`   |
|  `bigint`   |    `java.lang.long`    |
|   `float`   |   `java.lang.float`    |
|  `dobule`   |   `java.lang.double`   |
|  `decimal`  | `java.math.BigDecimal` |
|   `date`    |    `java.sql.date`     |
|    time     |    `java.sql.time`     |
| `datetime`  |    `java.sql.date`     |
| `timestamp` |  `java.sql.timestamp`  |

---

## 3 DAO设计

### 3.1 DAO思想

==什么是DAO思想？==

**DAO(Data Access Object)**是一个数据访问接口

**数据访问**：顾名思义就是与数据库打交道，夹在业务逻辑与数据库资源中间。==封装了数据库的增删改查(CRUD)操作。==

==为什么得有DAO思想？==

- DAO思想出现之前是怎么样的？
  - 有一个操作就要写一段代码，未遵循DRY(Don’t Repeat Yourself)原则。
- DAO思想出现之后又是怎么样的？
  - 在核心J2EE模式中是这样介绍DAO模式的：为了建立一个健壮的J2EE应用，应该将所有对数据库访问的操作抽象封装在一个公共的api中。
  - 用程序设计的语言来说，就是建立一个接口，接口中定义了此应用程序中将会用到的所有事务方法。在这个应用程序中，当需要和数据源进行交互的时候则使用这个接口，并且编写一个单独的类来实现这个接口在逻辑上对应这个特定的数据存储。

**分析DAO组件中的方法该如何具体设计：==方法的参数和返回==**

**分析保存方法**

- void save(Student student );根据封装的思想可以封装成一个对象不要传具体属性

**分析查询方法(单个)**

- Student get(Long id);

==domian的作用：数据的中转==

---

### 3.2 DAO设计

**DAO设计规范**

---

**DAO组件**

- DAO接口
- DAO实现类
- *DAO测试类*

**包名的规范**

- package 域名倒写.模块名称.组件名称
  - com.domain：存储所有的domain对象
  - com.dao：存储所有的DAO接口
  - com.dao.impl：存储所有的DAO接口实现类
  - com.test:存储DAO组件的测试类

**类名和接口规范**

- domain类存储在domain包中，用于描述一个个对象，是一个JavaBean包含字段和getter/setter
- dao接口:用于表示某一个对象的CRUD操作
- dao实现类：类存储在dao下面的impl，用于表示一个DAO类的实现类，必须要实现DAO接口
- dao测试类：根据DAO来生成的测试类

**开发步骤**

- 创建一张表用来存储信息
- 建立domain包和domain类
- 建立dao包和dao接口
- 建立dao.impl包和dao实现类dao.impl
- 根据dao接口创建一个dao测试类
- 编写impl中的每一个dao方法的实现
- 测试功能是否正确

---

### 3.3 DAO实现

**实体对象**

```java
@Setter
@Getter
public class Student implements Serializable{

	/**
	 * 
	 */
	private static final long serialVersionUID = -5978743651356272995L;
	
	private Long id;
	
	private String name;//姓名
	
	private Integer age;//年龄

	@Override
	public String toString() {
		return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
	}
}
```

**DAO接口**

```java
//封装Student的CRUD操作
public interface IStudentDao {
	/**
	 * 保存方法
	 * @param student 学生的信息
	 */
	public void save(Student student);
	
	/**
	 * 删除操作
	 * @param id 该学生的主键值
	 */
	public void delete(Long id);

	/**
	 * 更新操作
	 * @param id 该学生的主键值
	 * @param student 学生的信息
	 */
	public void update(Long id,Student student);
	
	/**
	 * 查询指定id的学生值
	 * @param id 该学生的主键值
	 * @return
	 */
	public Student get(Long id);

	/**
	 * 获取全部的学生
	 * @return
	 */
	public List<Student> findAll();
}
```

==面向接口编程==

---

### 3.4 使用Statement实现的DAO

#### 3.4.1 impl类

```java
public class StudentDaoImpl implements IStudentDao {

	@Override
	public void save(Student student) {
		String sql = "INSERT INTO `t_student` ( `name`, `age`) VALUES ( '" + student.getName() + "', '"
				+ student.getAge() + "');";
		Statement statement = null;
		Connection connection = null;
		String url = "jdbc:mysql://localhost:3306/clouddb01";
		String user = "root";
		String passWord = "steven123";
		try {
			// 加载注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获数据库连接对象
			connection = DriverManager.getConnection(url, user, passWord);
			// 创建语句对象
			statement = connection.createStatement();
			// 执行SQL语句
			statement.executeUpdate(sql);
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (null != statement) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if (null != connection) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}

	}

	@Override
	public void delete(Long id) {
		String sql = "delete from t_student where id =" + id;
		Statement statement = null;
		Connection connection = null;
		String url = "jdbc:mysql://localhost:3306/clouddb01";
		String user = "root";
		String passWord = "steven123";
		try {
			// 加载注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获数据库连接对象
			connection = DriverManager.getConnection(url, user, passWord);
			// 创建语句对象
			statement = connection.createStatement();
			System.out.println(sql);
			// 执行SQL语句
			statement.executeUpdate(sql);
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (null != statement) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if (null != connection) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}

	@Override
	public void update(Long id, Student student) {
		String sql = "update t_student set `name` = '" + student.getName() + "',age='" + student.getAge() +"' where id =" + id;
		Statement statement = null;
		Connection connection = null;
		String url = "jdbc:mysql://localhost:3306/clouddb01";
		String user = "root";
		String passWord = "steven123";
		try {
			// 加载注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获数据库连接对象
			connection = DriverManager.getConnection(url, user, passWord);
			// 创建语句对象
			statement = connection.createStatement();
			System.out.println(sql);
			// 执行SQL语句
			statement.executeUpdate(sql);
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (null != statement) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if (null != connection) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}

	}

	@Override
	public Student get(Long id) {
		String sql = "select * from t_student where id =" + id;
		Statement statement = null;
		Connection connection = null;
         ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/clouddb01";
		String user = "root";
		String passWord = "steven123";
		Student student = new Student();
		try {
			// 加载注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获数据库连接对象
			connection = DriverManager.getConnection(url, user, passWord);
			// 创建语句对象
			statement = connection.createStatement();
			// 执行SQL语句
			ResultSet rs = statement.executeQuery(sql);
			if (rs.next()) {
				student.setId(rs.getLong(1));
				student.setName(rs.getString(2));
				student.setAge(rs.getInt(3));
				return student;
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
            if (null != rs) {
				try {
					rs.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
            
			if (null != statement) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if (null != connection) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		return null;
	}

	@Override
	public List<Student> findAll() {

		String sql = "select * from t_student";
		Statement statement = null;
		Connection connection = null;
         ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/clouddb01";
		String user = "root";
		String passWord = "steven123";
		List<Student> students = new ArrayList<Student>();
		try {
			// 加载注册驱动
			Class.forName("com.mysql.jdbc.Driver");
			// 获数据库连接对象
			connection = DriverManager.getConnection(url, user, passWord);
			// 创建语句对象
			statement = connection.createStatement();
			// 执行SQL语句
			ResultSet rs = statement.executeQuery(sql);

			while (rs.next()) {
				Student student = new Student();
				student.setId(rs.getLong(1));
				student.setName(rs.getString(2));
				student.setAge(rs.getInt(3));
				students.add(student);
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
            if (null != rs) {
				try {
					rs.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
            
			if (null != statement) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if (null != connection) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		return students.size() <= 0 ? null : students;

	}

}
```

---

#### 3.4.2 Test类

```java
//DAO组件的测试类，所以依赖DAO组件
public class StudentDaoTest {

	private IStudentDao studentDao = new StudentDaoImpl();
	@Test
	public void testSave() {
		Student student = new Student();
		student.setName("steven");
		student.setAge(28);
		studentDao.save(student);
	}

	@Test
	public void testDelete() {
		studentDao.delete(2L);
	}

	@Test
	public void testUpdate() {
		Student student = new Student();
		student.setName("测试");
		student.setAge(29);
		studentDao.update(2L, student);
	}

	@Test
	public void testGet() {
		Student student = studentDao.get(3L);
		System.out.println(student);
	}

	@Test
	public void testFindAll() {
		List<Student> students = studentDao.findAll();
		for (Student student : students) {
			System.out.println(student);

		}
	}

}
```

---

## 4 重构设计

**重构(Refactoring)**就是通过**调整程序代码**，改善软件的质量、性能，使其程序的设计模式和架构更趋合理，==提高软件的扩展性和维护性==。

**连接数据库四要素**

- 驱动名
- URL
- 用户名
- 密码

优化：声明成成员变量

```java
//连接数据库的四要素
private String driverName = "com.mysql.jdbc.Driver";
private String url = "jdbc:mysql://localhost:3306/clouddb01";
private String user = "root";
private String passWord = "steven123";
```

每个`DAO`有这4个方法

优化：编写一个`JdbcUtil`

---

#### 4.1 抽取JdbcUtil类

```java
public class JdbcUtil {
	// 连接数据库的四要素
	public static final String driverName = "com.mysql.jdbc.Driver";
	public static final  String url = "jdbc:mysql://localhost:3306/clouddb01";
	public static final  String user = "root";
	public static final  String passWord = "steven123";
}
```

```java
// 加载注册驱动
Class.forName(JdbcUtil.driverName);
// 获数据库连接对象
connection = DriverManager.getConnection(JdbcUtil.url, JdbcUtil.user, JdbcUtil.passWord);
```

用public破坏封装有被篡改的风险

解决方案：把获取Connection方法放到`JdbcUtil`

```java
public class JdbcUtil {
	// 连接数据库的四要素
	private static final String driverName = "com.mysql.jdbc.Driver";
	private static final String url = "jdbc:mysql://localhost:3306/clouddb01";
	private static final String user = "root";
	private static final String passWord = "steven123";

	public static Connection getConnection() {
		try {
			// 加载注册驱动
			Class.forName(driverName);
			// 获数据库连接对象
			return DriverManager.getConnection(url, user, passWord);
		} catch (Exception e) {
			e.printStackTrace();
		}

		return null;
	}
}
```

```java
// 获数据库连接对象
connection = JdbcUtil.getConnection();
```

驱动重复注入每次调用都需要重复注册

优化方案：放入static代码块

```java
static {
	try {
		//当JdbcUtil这份字节码被加载进JVM就立即执行
		//加载注册驱动
		Class.forName(driverName);
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
	}
}
```

每次关闭的代码太长

优化思路：放到`JdbcUtil`中

```java
public static void close(ResultSet rs, Statement statement, Connection connection) {
		if (null != rs) {
			try {
				rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
		if (null != statement) {
			try {
				statement.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

		if (null != connection) {
			try {
				connection.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
```

```java
//DML
JdbcUtil.close(null, statement, connection);
//DQL
JdbcUtil.close(rs, statement, connection);
```

---

#### 4.2 抽取db.properties文件

`JdbcUtil`类存在硬编码(**数据库连接四要素**)，不利于维护

解决方案：把数据库的信息专门的提取

到配置文件中去，那么以后只要改配置文件即可。

```properties
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/clouddb01
user = root
passWord = steven123
```

```java
	private static Properties properties =  new Properties();
	static {
		try {
			InputStream inputStream = Thread.currentThread().getContextClassLoader().getResourceAsStream("jdbc.properties");
			properties.load(inputStream);
			// 当JdbcUtil这份字节码被加载进JVM就立即执行
			// 加载注册驱动
			Class.forName(properties.getProperty("driver"));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```

#### 4.3 JdbcTemplate的DML操作模板

```java
/**
 * DML操作(增删改)的模板
 * @param sql    DML操作的SQL的模板(带有占位符)
 * @param params SQL模板中对应的参数值
 * @return       受影响的行数
 * @throws Exception
 */
public static int update(String sql, Object... params) {
	Connection connection = null;
	PreparedStatement ps = null;
	try {
		DataSource ds = getPropertiesDataSource();
		connection = ds.getConnection();
		ps = connection.prepareStatement(sql);
		for (int i = 0; i < params.length; i++) {
			ps.setObject(i + 1, params[i]);
		}
		return ps.executeUpdate();
	} catch (Exception e) {
		e.printStackTrace();
	}
	return 0;
}
```

---

#### 4.4 JdbcTemplate的DQL操作模板-低级版

```java
/**
 * 查询操作的模板
 * 
 * @param sql    DQL操作的SQL的模板(带有占位符)
 * @param params SQL模板中对应的参数值
 * @return 	     List集合
 * @throws Exception
 */
public static List<Student> query(String sql, Object... params) {
	List<Student> students = new ArrayList<Student>();
	Connection connection = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	try {
		DataSource ds = getPropertiesDataSource();
		connection = ds.getConnection();
		ps = connection.prepareStatement(sql);
		for (int i = 0; i < params.length; i++) {
			ps.setObject(i + 1, params[i]);
		}
		rs = ps.executeQuery();
		while (rs.next()) {
			Student student = new Student();
			student.setId(rs.getLong("id"));
			student.setName(rs.getString("name"));
			student.setAge(rs.getInt("age"));
			students.add(student);
		}
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JdbcUtils.close(rs);
		JdbcUtils.close(ps);
		JdbcUtils.close(connection);
	}
	return students;
}
```

---

#### 4.5 JdbcTemplate的DQL操作模板-升级版

在低级版的DQL操作模板，只能针对于

- 把t_student表中的每一行数据封装成student对象
- 而在开发中，我们需要处理N张表，**说明此时的query根本就不通用**

**解决方案：**

- JdbcTemplate的query方法在处理结果集的时候，不知道把每一行数据封装成什么类型的对象，query方法就不应该来处理结果集，我们可以把处理结果集的行为交给每个对象的DAO来处理。
- 为了避免不同DAO实现类定义的处理结果集的方法和名字不同，我们来指定规范

```java
public interface IResultSetHandler {
	//处理结果集
	List handler(ResultSet rs) throws Exception;
}
```

```java
//把结果集每一行数据封装成student对象
class studentResultSetHandler implements IResultSetHandler {

	@Override
	public List handler(ResultSet rs) throws Exception {
		List students = new ArrayList<Student>();
		while (rs.next()) {
			Student student = new Student();
			student.setId(rs.getLong("id"));
			student.setName(rs.getString("name"));
			student.setAge(rs.getInt("age"));
			students.add(student);
		}
		return students;
	}

}
```

```java
@Override
public List<Student> findAll() {
	String sql = "select * from  `t_student`";
	return JdbcTemplate.query(sql,new studentResultSetHandler());
}
```

---

#### 4.6 JdbcTemplate的DQL操作模板-高级版

在升级版代码存在的一些问题：

- 很多黄色的警告，主要原因是没有给集合使用泛型
- 上述代码JdbcTemplate的query方法最终返回一个List集合不是很合理

```java
import java.sql.ResultSet;

public interface IResultSetHandler<T> {
	//处理结果集 表示使用T作为一种未知类型，最后处理完结果集就是该类型，而该类型由方法调用者指定
	T handler(ResultSet rs) throws Exception;
}
```

```java
//把结果集每一行数据封装成student对象
class studentResultSetHandler implements IResultSetHandler<List<Student>> {

	@Override
	public List<Student> handler(ResultSet rs) throws Exception {
		List<Student> students = new ArrayList<Student>();
		while (rs.next()) {
			Student student = new Student();
			student.setId(rs.getLong("id"));
			student.setName(rs.getString("name"));
			student.setAge(rs.getInt("age"));
			students.add(student);
		}
		return students;
	}
}
```

```java
/**
 * 查询操作的模板
 * @param sql    DQL操作的SQL的模板(带有占位符)
 * @param params SQL模板中对应的参数值
 * @return List集合
 * @throws Exception
*/
public static <T>T query(String sql,IResultSetHandler<T> resultSetHandler,Object... params) {
	Connection connection = null;
	PreparedStatement ps = null;
	ResultSet rs = null;
	try {
		DataSource ds = getPropertiesDataSource();
		connection = ds.getConnection();
		ps = connection.prepareStatement(sql);
		for (int i = 0; i < params.length; i++) {
			ps.setObject(i + 1, params[i]);
		}
		rs = ps.executeQuery();
		return resultSetHandler.handler(rs);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JdbcUtils.close(rs);
		JdbcUtils.close(ps);
		JdbcUtils.close(connection);
	}
		throw new RuntimeException("查询错误");
}
```

```java
@Override
public List<Student> findAll() {
	String sql = "select * from  `t_student`";
	return JdbcTemplate.query(sql,new studentResultSetHandler());
}
```

---

#### 4.7 通用结果集处理器-BeanHandler

把结果集的一行数据，封装成一个对象，专门针对结果集中只有一行数据的情况

```java
import java.beans.BeanInfo;
import java.beans.Introspector;
import java.beans.PropertyDescriptor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.sql.ResultSet;
import com.alibaba.druid.util.StringUtils;
public class BeanHandler<T> implements IResultSetHandler<T> {
	private Class<T> classType;// 把结果集中的一行数据，封装成什么类型的对象

	private static final String SET_PREFIX = "set";
	private static final String IGNORE_FIELD = "serialVersionUID";

	public BeanHandler(Class<T> classType) {
		this.classType = classType;
	}

    //方法一
	@Override
	public T handler(ResultSet rs) throws Exception {
		Class<T> clazz = classType;
		T obj = clazz.newInstance();
		Field[] fields = clazz.getDeclaredFields();
		if (rs.next()) {
			for (Field field : fields) {
				String columnName = field.getName();
				Object val = null;
				if (!StringUtils.equals(columnName, IGNORE_FIELD)) {
					String methodEnd = columnName.substring(0, 1).toUpperCase() + columnName.substring(1);
					Method setMethod = clazz.getDeclaredMethod(SET_PREFIX + methodEnd, new Class[] { field.getType() });
					val = rs.getObject(columnName);
					setMethod.invoke(obj, val);
					System.out.println("columnName:" + columnName + "\t val:" + val);
				}
				
			}
		}

		return obj;
	}
    
    //方法二
	public T handlerReflect(ResultSet rs) throws Exception {
		T obj = classType.newInstance();

		BeanInfo beanInfo = Introspector.getBeanInfo(classType, Object.class);
		PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();
		if (rs.next()) {
			for (PropertyDescriptor pd : pds) {
				String columnName = pd.getName();
				Object val = rs.getObject(columnName);
				System.out.println("columnName:" + columnName + "\t val:" + val);
				pd.getWriteMethod().invoke(obj, val);
			}
		}

		return obj;
	}

}
```



---

#### 4.8 通用结果集处理器-BeanListHandler

把结果集的多行数据，封装成一个对象的集合(List<xxx>)

```java
import java.beans.BeanInfo;
import java.beans.Introspector;
import java.beans.PropertyDescriptor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;
import com.alibaba.druid.util.StringUtils;
public class BeanListHandler<T> implements IResultSetHandler<List<T>> {
	private Class<T> classType;// 把结果集中的一行数据，封装成什么类型的对象

	private static final String SET_PREFIX = "set";
	private static final String IGNORE_FIELD = "serialVersionUID";

	public BeanListHandler(Class<T> classType) {
		this.classType = classType;
	}

    //方法一
	@Override
	public List<T> handler(ResultSet rs) throws Exception {
		List<T> list = new ArrayList<>();

		Class<T> clazz = classType;
		T obj = clazz.newInstance();
		Field[] fields = clazz.getDeclaredFields();
		while (rs.next()) {
			for (Field field : fields) {
				String columnName = field.getName();
				Object val = null;
				if (!StringUtils.equals(columnName, IGNORE_FIELD)) {
					String methodEnd = columnName.substring(0, 1).toUpperCase() + columnName.substring(1);
					Method setMethod = clazz.getDeclaredMethod(SET_PREFIX + methodEnd, new Class[] { field.getType() });
					val = rs.getObject(columnName);
					setMethod.invoke(obj, val);
					System.out.println("columnName:" + columnName + "\t val:" + val);
					list.add(obj);
				}

			}
		}
		return list;
	}

    //方法二
	public List<T> handlerReflect(ResultSet rs) throws Exception {
		List<T> list = new ArrayList<>();

		T obj = classType.newInstance();

		BeanInfo beanInfo = Introspector.getBeanInfo(classType, Object.class);
		PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();
		while (rs.next()) {
			for (PropertyDescriptor pd : pds) {
				String columnName = pd.getName();
				Object val = rs.getObject(columnName);
				System.out.println("columnName:" + columnName + "\t val:" + val);
				pd.getWriteMethod().invoke(obj, val);
			}
			list.add(obj);
		}

		return list;
	}
}
```

---

#### 4.9 处理不属于BeanHandler和BeanListHandler

```java
@Override
public Long conunt() {
	String sql = "select count(*) from  `t_student`";
	Long count = JdbcTemplate.query(sql, new IResultSetHandler<Long>() {
		@Override
		public Long handler(ResultSet rs) throws Exception {
			if(rs.next()) {
				return rs.getLong(1);
			}
			return 0L;
		}
	});
	return count;
}
```



---

## 5 预编译语句对象

#### 5.1 基本操作

==`DAO`方法中拼接方法容易出错==

解决方案：`PreparedStatement`

Statement

- 表示静态SQL语句对象。 

PreparedStatement

- Statement子接口，表示预编译的SQL语句的对象，通过占位符来拼接SQL。
- PreparedStatement prepareStatement(String sql) ：创建预编译语句对象。
  - 参数表示带有占位符的`SQL`
  - `void setxxx xxx`表示数据类型 比如 int，string ==index从1开始==

==可以提高性能 方法不需要传入`SQL` 创建预编译对象时 已经传入==

---

##### 5.1.1 实现插入数据库操作

```java
public class PreparedStatementTest {
	@Test
	public void insertTest() throws Exception {
		String sql = "INSERT INTO `t_student` (`name`, `age`) VALUES (?, ?);";
		Connection connection = JdbcUtil.getConnection();
		PreparedStatement preparedStatement = connection.prepareStatement(sql);
		preparedStatement.setString(1, "BB");
		preparedStatement.setInt(2, 16);
		int result = preparedStatement.executeUpdate();
		System.out.println(result);
		JdbcUtil.close(null, preparedStatement, connection);
	}
}
```

---

##### 5.1.2 使用PreparedStatement实现DAO

```java
public class StudentPSDaoImpl implements IStudentDao {

	@Override
	public void save(Student student) {
		String sql = "INSERT INTO `t_student` (`name`, `age`) VALUES (?, ?);";
		PreparedStatement preparedStatement = null;
		Connection connection = null;
		try {
			connection = JdbcUtil.getConnection();
			preparedStatement = connection.prepareStatement(sql);
			preparedStatement.setString(1, student.getName());
			preparedStatement.setInt(2, student.getAge());
			preparedStatement.executeUpdate();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			JdbcUtil.close(null, preparedStatement, connection);
		}
	}

	@Override
	public void delete(Long id) {
		String sql = "delete from `t_student` where id = ?;";
		PreparedStatement preparedStatement = null;
		Connection connection = null;
		try {
			connection = JdbcUtil.getConnection();
			preparedStatement = connection.prepareStatement(sql);
			preparedStatement.setLong(1, id);
			preparedStatement.executeUpdate();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			JdbcUtil.close(null, preparedStatement, connection);
		}

	}

	@Override
	public void update(Long id, Student student) {
		String sql = "update `t_student` set name = ?,age = ? where id = ?;";
		PreparedStatement preparedStatement = null;
		Connection connection = null;
		try {
			connection = JdbcUtil.getConnection();
			preparedStatement = connection.prepareStatement(sql);
			preparedStatement.setString(1, student.getName());
			preparedStatement.setInt(2, student.getAge());
			preparedStatement.setLong(3, id);
			preparedStatement.executeUpdate();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			JdbcUtil.close(null, preparedStatement, connection);
		}

	}

	@Override
	public Student get(Long id) {
		String sql = "select * from  `t_student` where id = ?;";
		PreparedStatement preparedStatement = null;
		Connection connection = null;
		ResultSet resultSet = null;
		Student student = null;
		try {
			connection = JdbcUtil.getConnection();
			preparedStatement = connection.prepareStatement(sql);
			preparedStatement.setLong(1, id);
			resultSet = preparedStatement.executeQuery();
			if(resultSet.next()) {
				student = new Student();
				student.setId(resultSet.getLong("id"));
				student.setName(resultSet.getString("name"));
				student.setAge(resultSet.getInt("age"));
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			JdbcUtil.close(resultSet, preparedStatement, connection);
		}
		return student;

	}

	@Override
	public List<Student> findAll() {
		String sql = "select * from  `t_student` ";
		PreparedStatement preparedStatement = null;
		Connection connection = null;
		ResultSet resultSet = null;
		List<Student> students = new ArrayList<Student>();
		try {
			connection = JdbcUtil.getConnection();
			preparedStatement = connection.prepareStatement(sql);
			resultSet = preparedStatement.executeQuery();
			while(resultSet.next()) {
				Student student = new Student();
				student.setId(resultSet.getLong("id"));
				student.setName(resultSet.getString("name"));
				student.setAge(resultSet.getInt("age"));
				students.add(student);
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			JdbcUtil.close(resultSet, preparedStatement, connection);
		}
		return students.size() < 0 ? null : students;
	}

}
```

---

#### 5.2 存在优势

##### 5.2.1 可读和维护性

PreparedStatement相对于Statement的优势

- 拼接SQL上操作更简单
- 性能会更加高效，但是需要取决于数据库服务器是否支持

---

##### 5.2.2 性能问题

SQL执行流程

- 安全性分析
- 语法分析
- 编译SQL语句
- 选择并执行一个计划
- 返回一个受影响行或一个结果集

对于某一些数据库服务器，存在着预编译池，每一条SQL进入DBMS的时候，首先会去判断预编译池中是否存在

- 存在
  - 直接执行第4部
- 不存在
  - 执行1，2，3，4，把编译之后的SQL存储到预编译池中

==`MySQL`不支持 `Oracle`支持==

---

##### 5.2.3 可防止SQL注入

Statement存在SQL注入问题

```java
public void login() {
	String sql = "select * from t_student where name='' or 1=1 and age = 28";
	Statement statement = null;
	Connection connection = null;
	ResultSet rs = null;
	try {
		// 获数据库连接对象
		connection = JdbcUtil.getConnection();
		// 创建语句对象
		statement = connection.createStatement();
		// 执行SQL语句
		rs = statement.executeQuery(sql);
		if (rs.next()) {
			System.out.println("登陆成功");
		}else {
			System.out.println("登陆失败");
		}

	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JdbcUtil.close(rs, statement, connection);
	}
}
```

==`' or 1=1 `实现`SQL`注入==

---

#### 5.3 登陆功能实现

```java
public void login() {
	String sql = "select * from  `t_student` where name = ? and age = ?";
	PreparedStatement preparedStatement = null;
	Connection connection = null;
	ResultSet resultSet = null;
	try {
		connection = JdbcUtil.getConnection();
		preparedStatement = connection.prepareStatement(sql);
		preparedStatement.setString(1, "steven");
		preparedStatement.setInt(2, 28);
		resultSet = preparedStatement.executeQuery();
		if (resultSet.next()) {
			System.out.println("登陆成功");
		}else {
			System.out.println("登陆失败");
		}
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		JdbcUtil.close(resultSet, preparedStatement, connection);
	}
}
```

---

## 6 事务管理操作

#### 6.1 银行转账案例

```java
@Test
public void testAccount() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	String sql = "select * from  account where id = ? and balance > ?";
	PreparedStatement ps = connection.prepareStatement(sql);
	ps.setLong(1, 1L);
	ps.setInt(2, 1000);
	ResultSet rs = ps.executeQuery();
	if (rs.next()) {
		System.out.println(rs.getLong("id"));
		System.out.println(rs.getString("name"));
		System.out.println(rs.getInt("balance"));
		sql = "update  account set balance = balance - ? where id = ?";
		ps = connection.prepareStatement(sql);
		ps.setInt(1, 1000);
		ps.setLong(2, 1L);
		ps.executeUpdate();
		int i =1/0;//代码异常后扣款正常 但是没有正常累加
	//===================================================================
		sql = "update  account set balance = balance + ? where id = ?";
		ps = connection.prepareStatement(sql);
		ps.setInt(1, 1000);
		ps.setLong(2, 2L);
		ps.executeUpdate();
	} else {
		System.out.println("余额不足!");
	}
	JdbcUtil.close(rs, ps, connection);
}
```

---

#### 6.2 事务概述和处理

**事务(Transaction，简写为`tx`)**：在数据库中，所谓事务是指一组逻辑操作，不论成功与失败都作为一个整体进行工作，要么全执行，要么全不执行。

处理事务的两个小动作

- 提交commit：当整个事务中，所有的逻辑操作都正常执行成功
- 回滚rollback：当整个事务中，有一个或多个逻辑操作执行失败。

事务的ACID原则

- 原子性(Atomicity)：事务是应用中不可再分的最小逻辑执行单位体，要么都执行，要么都不执行。
- 一致性(Consistency)：事务结束后，数据库内的数据是合法正确的。(数据不被破坏)
- 隔离性(Isolation)：并发执行的事务之间彼此相互独立，互不干扰。
- 持久性(Durability)：事务提交后，数据是永久性的，不可回滚。

```java
@Test
public void testAccountTransaction() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	PreparedStatement ps = null;
	ResultSet rs = null;
	try {
		String sql = "select * from  account where id = ? and balance > ?";
		ps = connection.prepareStatement(sql);
		ps.setLong(1, 1L);
		ps.setInt(2, 1000);
		rs = ps.executeQuery();
		if (rs.next()) {
			connection.setAutoCommit(false);//关闭自动提交
			System.out.println(rs.getLong("id"));
			System.out.println(rs.getString("name"));
			System.out.println(rs.getInt("balance"));
			sql = "update  account set balance = balance - ? where id = ?";
			ps = connection.prepareStatement(sql);
			ps.setInt(1, 1000);
			ps.setLong(2, 1L);
			ps.executeUpdate();
			int i = 1 / 0;// 代码异常后扣款正常 但是没有正常累加
// ===================================================================
			sql = "update  account set balance = balance + ? where id = ?";
			ps = connection.prepareStatement(sql);
			ps.setInt(1, 1000);
			ps.setLong(2, 2L);
			ps.executeUpdate();
			connection.commit();//全部处理完成提交
		} else {
			System.out.println("余额不足!");
		}
	} catch (Exception e) {
		e.printStackTrace();
		connection.rollback();//异常回滚
	} finally {
		JdbcUtil.close(rs, ps, connection);
	}
}
```

**如何在代码中处理事务**

- 在JDBC中缺省情况下，事务是自动提交的，控制事务必须先设置事务为手动提交
  - `connection.setAutoCommit(false);`
- 手动提交事务
  - connection.commit();
- 若出现异常必须回滚事务，不回滚事务，不会释放数据库资源
  - connection.rollback();

在JDBC在事务是默认提交的，那是在什么时候提交的

- 在执行一个DML/DDL操作的时候就已经提交事务了

针对CRUD操作，只有DML操作才有事务，==查询操作不需要事务==

以后凡是发现自己编写的代码是正确的，测试也通过，但是数据表中的数据不变--->事务没有提交

MYSQL中innoDB支持外键，支持事务，MyISAM不支持外键，不支持事务

- 修改数据库引擎：ALTER TABLE account ENGINE = 'myisam' 

---

## 7 批处理操作

#### 7.1 Statement方式

```java
@Test
public void testBatchStatement() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	long begin = System.currentTimeMillis();
	Statement statement = connection.createStatement();
	for (int i = 1; i <= 100; i++) {
		String sql = "INSERT INTO `t_student` (`name`, `age`) VALUES ('AA', 12)";
		statement.addBatch(sql);//添加进批处理
		if (i % 20 == 0) {
			statement.executeBatch();//执行操作
			statement.clearBatch();//清楚缓存
		}
	}
    JdbcUtil.close(null, statement, connection);
	System.out.println(System.currentTimeMillis() - begin);
}
```

---

#### 7.2 PreparedStatement方式

```java
@Test
public void testBatchPreparedStatement() throws Exception {
	String sql = "INSERT INTO `t_student` (`name`, `age`) VALUES (?, ?)";
	Connection connection = JdbcUtil.getConnection();
	long begin = System.currentTimeMillis();
	PreparedStatement preparedStatement = connection.prepareStatement(sql);
	for (int i = 1; i <= 100; i++) {
		preparedStatement.setString(1, "steven"+i);
		preparedStatement.setInt(2, i);
		preparedStatement.addBatch();//添加进批处理
		if (i % 20 == 0) {
			preparedStatement.executeBatch();//执行操作
			preparedStatement.clearBatch();//清楚缓存
			preparedStatement.clearParameters();//清楚参数
		}
	}
    JdbcUtil.close(null, preparedStatement, connection);
	System.out.println(System.currentTimeMillis() - begin);
}
```

==在JDBC中MySQL不支持批量操作==

```properties
?rewriteBatchedStatements=true
```

**对PreparedStatement方式有效 从5.1.13开始有效**

---

## 8 大数据类型操作

|    类型    |    允许的长度     |       存储空间       |
| :--------: | :---------------: | :------------------: |
|  char(M)   |         M         |  M为0~255之间的整数  |
| varchar(M) |         M         | M为0~65535之间的整数 |
|  tinytext  |    0~255 字节     |  值的长度加2个字节   |
|    text    |   0~65535 字节    |  值的长度加2个字节   |
| mediumint  | 0~167772150 字节  |  值的长度加3个字节   |
|  longtext  | 0~4294967295 字节 |  值的长度加4个字节   |

---

### 8.1 BLOB类型

BLOB是可变的数据类型，一般的开发中不会把二进制的文件(图片，音频，视频)存放在数据库中，一般把文件存储的路径保持在数据库中，那么以后要获取，只需从某个路径去找即可

举例：文件上传

- 不会把文件的二进制流存放在数据库中，而是把文件在服务器中的保存路径存在表中，如/update/xxx.jpg

|    类型    |  存储空间   |
| :--------: | :---------: |
|  tinyblob  |  255个字节  |
|    blob    | 65535个字节 |
| mediumblob |     16M     |
|  longblob  |  4G(变长)   |

```mysql
CREATE TABLE `t_image` (
  `id` bigint(20) NOT NULL,
  `img` longblob,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

```java
/**
 * 把image表中的图片数据保存到磁盘中
 * @throws Exception
*/
@Test
public void testSaveBLOB() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	String sql = "INSERT INTO t_image (img) VALUES (?)";
	PreparedStatement preparedStatement = connection.prepareStatement(sql);
	preparedStatement.setBlob(1, new FileInputStream("C:\\Users\\steven\\Pictures\\11.png"));
	preparedStatement.executeUpdate();
	JdbcUtil.close(null, preparedStatement, connection);
}
	
/**
 * 读取image文件
 * @throws Exception
*/
@Test
public void testReadBLOB() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	String sql = "select * from t_image where id = ?";
	PreparedStatement preparedStatement = connection.prepareStatement(sql);
	preparedStatement.setLong(1, 1);
	ResultSet resultSet = preparedStatement.executeQuery();
	if (resultSet.next()) {
		System.out.println(resultSet.getInt("id"));
		Blob blob = resultSet.getBlob("img");
		InputStream inputStream = blob.getBinaryStream();
		//文件copy操作
		Files.copy(inputStream, Paths.get("d:\\123.png"));
	}
	JdbcUtil.close(resultSet, preparedStatement, connection);
}
```

---

### 8.2 TEXT类型

MySQL中的TEXT类型和varchar是一样的，就仅仅是数据容量不一样而已一般用于存储大文本

在JAVA代码中，代码都是相同的，都是作为String类型的

|    类型    |  存储空间   |
| :--------: | :---------: |
|  tinytext  |  255个字节  |
|    text    | 65535个字节 |
| mediumtext |     16M     |
|  longtext  |  4G(变长)   |

---

## 9 获取自动生成的主键

### 9.1 为什么要获取主键

需要在多个页面传递主键

---

### 9.2 Statement方式

```java
@Test
public void testStatementGetId() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	String sql = "INSERT INTO t_student (name,age) VALUES ('aa',20)";
	Statement statement = connection.createStatement();
	//设置可获取自动生成的主键
	statement.executeUpdate(sql,Statement.RETURN_GENERATED_KEYS);
	//获取自动生成的主键
	ResultSet resultSet = statement.getGeneratedKeys();
	if(resultSet.next()) {
		Long id = resultSet.getLong(1);
		System.out.println(id);
	}
	JdbcUtil.close(resultSet, statement, connection);
}
```



---

### 9.3 PreparedStatement方式

```java
@Test
public void testPreparedStatementGetId() throws Exception {
	Connection connection = JdbcUtil.getConnection();
	String sql = "INSERT INTO t_student (name,age) VALUES (?,?)";
	// 设置可获取自动生成的主键
	PreparedStatement preparedStatement = connection.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
	preparedStatement.setString(1, "kk");
	preparedStatement.setInt(2, 32);
	preparedStatement.executeUpdate();
	// 获取自动生成的主键
	ResultSet resultSet = preparedStatement.getGeneratedKeys();
	if (resultSet.next()) {
		Long id = resultSet.getLong(1);
		System.out.println(id);
	}
	JdbcUtil.close(null, preparedStatement, connection);
}
```

---

## 10 连接池：DataSource

### 10.1 连接池思想

为什么必须使用数据库连接池

- **数据库连接(Connection)是一种关键的、有限的、昂贵的资源。**
- 普通的JDBC数据库**连接使用DriverManager来获取**，每次向数据库建立连接的时候都要将Connection加载到内存中，再验证用户名和密码(**得花费0.05S~1S的时间**)。
- 需要数据库连接池的时候，就像数据库要求一个，执行完成后再断开连接。这样的方式将会消耗大量的资源和时间。
- 数据库的连接资源并没有得到很好的重复利用，若同时有几百人甚至几千人在线，频繁的进行数据库连接操作将占用很多的系统资源，严重的甚至会造成服务器的崩溃。
- 对于每一次数据库连接，使用完后都得断开，否则，如果程序出现异常而未能关闭，将会导致数据库系统中的内存泄露，最终导致重启数据库。
- 这种开发不能控制被专家的连接对象数。系统资源会被毫无顾忌的分配出去，如果连接多，也可能导致内存泄露，服务器崩溃。

连接池具有属性

- 连接数据库四要素
- 初始连接数
  - 5 最开始连接池中存在5个connection对象
- 最大连接数
  - 10 连接池中最多连接10个connection对象
- 最小连接数
  - 3 连接池中最少将存在3个connection对象
- 最大空闲时间
  - 2s，如果获取connection对象之后，在2分钟呢，没有操作，就自动释放连接
- 最大等待时间
  - 30s,在30秒之内尝试获取连接，如果过了30秒，还没获取到就放弃

**连接池的概念就是缓存**

---

### 10.2 连接池概述

在`java`中，连接池使用`javax.sql.DataSource`接口表示连接池

注意：DataSource仅仅只是一个接口，由第三方组织提供

常用的DataSource的实现

- DBCP：Spring推荐
- C3P0：Hibernate推荐的 2007年不在维护 ==性能较差==

DataSource数据源：和连接池connection pool是同一个

如何获取连接

- 没有连接池
  - 使用DrvicerManager来获取，Connection coon = DrvicerManager.getConnection(url,user,password)
- 使用连接池
  - 通过DateSource对象.getConnectionConnection coon = DateSource.getConnection(url,user,password)

如何释放连接

- 没有连接池
  - coon.close() 和数据库断开连接
- 使用连接池
  - coon.close() 把连接对象还给连接池，还可以再使用。

---

### 10.3 手写模拟连接池

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.sql.Connection;

public class MyConnectionHandler implements InvocationHandler{
	
	private Connection connection;
	
	private Connection myConnection;
	
	private void bind(Connection connection) {
		this.connection = connection;
	}
	// connection 被代理对象
	public Connection getProxyInstance(Connection connection) { 
		bind(connection);
		myConnection = (Connection) Proxy.newProxyInstance(this.getClass().getClassLoader(), new Class[] {Connection.class}, this);
		return myConnection;
	}


	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		Object obj = null;
		if(method.getName().equals("close")) {
			JdbcUtil.currentUserCount.incrementAndGet();
			if(JdbcUtil.currentUserCount.get() < JdbcUtil.maxUserCount) {
				JdbcUtil.connectionsPool.addLast(myConnection);
			}else {
				connection.close();
				JdbcUtil.currentCount.decrementAndGet();
				JdbcUtil.currentUserCount.set(0);
			}
			
		}else {
			obj = method.invoke(connection, args);
		}
		return obj;
	}
}

```

```java
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.LinkedList;
import java.util.Properties;
import java.util.concurrent.atomic.AtomicInteger;

public class JdbcUtil {
	private static Properties properties = new Properties();
	public static LinkedList<Connection> connectionsPool = new LinkedList<Connection>();
	private static int initCount = 10;
	private static int maxCount = 10;
	public static AtomicInteger currentCount = new AtomicInteger();
	public static int maxUserCount = 5;
	public static AtomicInteger currentUserCount = new AtomicInteger();


	static {
		try {
			InputStream inputStream = Thread.currentThread().getContextClassLoader().getResourceAsStream("jdbc.properties");
			properties.load(inputStream);
			// 当JdbcUtil这份字节码被加载进JVM就立即执行
			// 加载注册驱动
			Class.forName(properties.getProperty("driver"));

			for (int i = 0; i < initCount; i++) {
				connectionsPool.addLast(createConnection());
				currentCount.getAndIncrement();
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public static Connection createConnection() {
		try {
			// 获数据库连接对象
			Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties.getProperty("user"),properties.getProperty("passWord"));
			MyConnectionHandler myConnectionHandler = new MyConnectionHandler();
			return myConnectionHandler.getProxyInstance(connection);
		} catch (Exception e) {
			e.printStackTrace();
		}

		return null;

	}

	public static Connection getConnection() throws Exception {
		synchronized (connectionsPool) {
			if(connectionsPool.size() > 0)
				return connectionsPool.removeFirst();
			else {
				if(currentCount.get() < maxCount) {
					return createConnection();
				}else {
					throw new Exception("已没有连接");
				}
			}
				
		}
	}

	public static void close(ResultSet rs, Statement statement, Connection connection) {
		if (null != rs) {
			try {
				rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

		if (null != statement) {
			try {
				statement.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

		if (null != connection) {
			try {
				connection.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}
```

---

### 10.4 dbcp连接池

```xml
<!-- dbcp2数据库连接池 -->
<dependency>
   <groupId>org.apache.commons</groupId>
   <artifactId>commons-dbcp2</artifactId>
</dependency>
```

#### 10.4.1 硬编码

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import javax.sql.DataSource;
import org.apache.commons.dbcp2.BasicDataSource;
import org.junit.Test;

public class DBCPTest {
	//创建一个连接池
	private DataSource getDataSource() {
		//创建连接对象
		BasicDataSource ds = new BasicDataSource();
		//设置数据库四要素
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUsername("root");
		ds.setPassword("steven123");
		ds.setUrl("jdbc:mysql://localhost:3306/clouddb01");
		ds.setMaxTotal(10);//最大连接数
		ds.setConnectionProperties("rewriteBatchedStatements=true");//参数拼接
		return ds;
	}
	
	@Test
	public void testDataSource() throws Exception{
		DataSource ds = getDataSource();
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while(rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}
}
```

---

#### 10.4.2 properties编码

配置都在`BasicDataSourceFactory`中

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Properties;
import javax.sql.DataSource;
import org.apache.commons.dbcp2.BasicDataSource;
import org.apache.commons.dbcp2.BasicDataSourceFactory;
import org.junit.Test;
public class DBCPTest {
	// 创建一个配置文件的连接池
	private DataSource getPropertiesDataSource() throws Exception {
		Properties properties = new Properties();
		properties.load(this.getClass().getClassLoader().getResourceAsStream("dbcp.properties"));
		// 通过BasicDataSourceFactory创建DataSource对象
		return BasicDataSourceFactory.createDataSource(properties);
	}


	@Test
	public void testPropertiesDataSource() throws Exception {
		DataSource ds = getPropertiesDataSource();
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}
}
```
```properties
#dbcp的属性配置
#key不能乱写 必须是BasicDataSource中的属性(setter方法)
driverClassName = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/clouddb01
username = root
password = steven123
maxTotal = 10
csetConnectionProperties = rewriteBatchedStatements=true
```

---

### 10.5 druid连接池

[druid](https://github.com/alibaba/druid)

#### 10.5.1 硬编码

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Properties;
import javax.sql.DataSource;
import org.junit.Test;
import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;
public class DruidTest {

	// 创建一个连接池
	private DataSource getDataSource() {
		// 创建连接对象
		DruidDataSource ds = new DruidDataSource();
		// 设置数据库四要素
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUsername("root");
		ds.setPassword("steven123");
		ds.setUrl("jdbc:mysql://localhost:3306/clouddb01");
		ds.setMaxActive(10);// 最大连接数
		ds.setConnectionProperties("rewriteBatchedStatements=true");// 参数拼接
		return ds;
	}

	@Test
	public void testDataSource() throws Exception {
		DataSource ds = getDataSource();
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}
}
```

---

#### 10.5.2 properties编码

```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
</dependency>
```

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.Properties;
import javax.sql.DataSource;
import org.junit.Test;
import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;
public class DruidTest {
	// 创建一个配置文件的连接池
	private DataSource getPropertiesDataSource() throws Exception {
		Properties properties = new Properties();
		properties.load(this.getClass().getClassLoader().getResourceAsStream("dbcp.properties"));
		// 通过BasicDataSourceFactory创建DataSource对象
		return DruidDataSourceFactory.createDataSource(properties);
	}

	@Test
	public void testPropertiesDataSource() throws Exception {
		DataSource ds = getPropertiesDataSource();
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}
}
```

```properties
#dbcp的属性配置
#key不能乱写
driverClassName = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/clouddb01
username = root
password = steven123
maxActive = 10
csetConnectionProperties = rewriteBatchedStatements=true
```

---

### 10.6 c3p0连接池

```xml
<dependency>
	<groupId>c3p0</groupId>
	<artifactId>c3p0</artifactId>
	<version>0.9.1.2</version>
</dependency>
```

---

#### 10.6.1 硬编码

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import javax.sql.DataSource;
import org.junit.Test;
import com.mchange.v2.c3p0.ComboPooledDataSource;
public class C3P0Test {

	// 创建一个连接池
	private DataSource getDataSource() throws Exception {
		// 创建连接对象
		ComboPooledDataSource  ds = new ComboPooledDataSource();
		// 设置数据库四要素
		ds.setDriverClass("com.mysql.jdbc.Driver");
		ds.setUser("root");
		ds.setPassword("steven123");
		ds.setJdbcUrl("jdbc:mysql://localhost:3306/clouddb01?rewriteBatchedStatements=true");
		ds.setMaxPoolSize(10);// 最大连接数
		return ds;
	}


	@Test
	public void testDataSource() throws Exception {
		DataSource ds = getDataSource();
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}
}
```

---

#### 10.6.2 xml编码

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import javax.sql.DataSource;
import org.junit.Test;
import com.mchange.v2.c3p0.ComboPooledDataSource;
public class C3P0Test {
	@Test
	// 创建一个c3p0-config.xml的配置文件
	public void testPropertiesDataSource() throws Exception {
		DataSource ds = new ComboPooledDataSource("test");
		Connection connection = ds.getConnection();
		PreparedStatement ps = connection.prepareStatement("select * from t_student");
		ResultSet rs = ps.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getLong("id"));
		}
	}

}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
	<!-- 默认配置，如果没有指定则使用这个配置 -->
	<default-config>
		<property name="user">root</property>
		<property name="password">steven123</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/clouddb01?rewriteBatchedStatements=true</property>
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="checkoutTimeout">30000</property>
		<property name="idleConnectionTestPeriod">30</property>
		<property name="initialPoolSize">3</property>
		<property name="maxIdleTime">30</property>
		<property name="maxPoolSize">100</property>
		<property name="minPoolSize">2</property>
		<property name="maxStatements">200</property>
	</default-config>
	<!-- 命名的配置,可以通过方法调用实现 -->
	<named-config name="test">
		<property name="user">root</property>
		<property name="password">steven123</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/clouddb01?rewriteBatchedStatements=true</property>
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<!-- 如果池中数据连接不够时一次增长多少个 -->
		<property name="acquireIncrement">5</property>
		<!-- 初始化数据库连接池时连接的数量 -->
		<property name="initialPoolSize">20</property>
		<!-- 数据库连接池中的最大的数据库连接数 -->
		<property name="maxPoolSize">25</property>
		<!-- 数据库连接池中的最小的数据库连接数 -->
		<property name="minPoolSize">5</property>
	</named-config>
</c3p0-config>
```

---

## 11 模拟Hibernate

```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;
import org.springframework.util.StringUtils;
import com.steven.domain.Table;
import jdk.nashorn.internal.ir.annotations.Ignore;
public class HibernateUtils {
	private static final String GET_PREFIX = "get";

	public static void save(Object obj) {
		try {
			StringBuffer sb = new StringBuffer("INSERT INTO ");
			Class<?> clazz = obj.getClass();
			Table table = clazz.getAnnotation(Table.class);
			String tableName = table.value();
			sb.append(tableName + " (");
			List<Object> list = new ArrayList<Object>();
			Field[] fields = clazz.getDeclaredFields();
			int i = 0;
			for (Field field : fields) {
				i++;
				Ignore annotation = field.getAnnotation(Ignore.class);
				if (StringUtils.isEmpty(annotation)) {
					Method method = clazz.getDeclaredMethod(GET_PREFIX + field.getName().substring(0, 1).toUpperCase() + field.getName().substring(1));
					Object val = method.invoke(obj);
					if (!StringUtils.isEmpty(val)) {
						if (i == fields.length)
							sb.append(field.getName() + ") VALUES (");
						else
							sb.append(field.getName() + ",");
						list.add(val);
					}
				}
			}

			if (list.size() > 0) {
				for (int j = 0; j < list.size(); j++) {
					sb.append("?,");
				}
			}
			sb = sb.deleteCharAt(sb.length() - 1);

			sb.append(")");

			JdbcTemplate.update(sb.toString(), list.toArray());
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```


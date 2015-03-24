## 个人网站搭建系列: Spring MVC, JPA, MySQL
### 小插曲
本系列文章，持续更新，关注浩的个人网站<http://imlinhao.cfapps.io/>，以及文章导引页 <http://blog.csdn.net/jksl007/article/details/44183559>

### 基础准备
#### 开发环境及工具
* Jdk 1.7及以上
* Maven 3.2及以上 [下载地址](http://maven.apache.org/download.cgi)
* Spring Boot CLI [下载地址](http://repo.spring.io/release/org/springframework/boot/spring-boot-cli/)
* WampServer (使用其MySQL服务) [下载地址](http://www.wampserver.com/en/)

#### 参考站点
* 本文源码 <https://github.com/imlinhao/website-mvc-jpa-mysql.git>
* Spring Boot Reference Guide <http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-use-a-higher-level-database-migration-tool>

#### 目标与方法描述
本文重点阐述如何，连接MySQL，以及如何在应用启动的时候，自动执行预先准备的SQL建表语句。

本文使用[flyway](http://flywaydb.org/)来自动执行建表语句。

### Spring Boot CLI初始化项目
[Spring Initializer](https://start.spring.io/)，提供了生成初始项目的简化方法。而使用Spring Boot CLI的`init`命令，则直接可以使用命令行生成初始项目。

	spring init --dependencies=web,data-jpa,mysql,thymeleaf website-mvc-jpa-mysql

此时，项目便有了基本的项目目录结构与所需的基本依赖。对于Spring Boot CLI所提供的依赖管理能力，可以通过`spring init --list`来查看。

### 使用flyway进行表创建
要使用flyway首先需要在已经生成的`pom.xml`添加依赖：

	<dependency>
		<groupId>org.flywaydb</groupId>
		<artifactId>flyway-core</artifactId>
	</dependency>

然后在`src/main/resources/db/migration`目录(需自行创建不存在的目录，后文给出的目录若不存在，也同样自行创建)下建立SQL建表语句，建表SQL的命名规则为`V<VERSION>__<NAME>.sql` ，如`V1__blog.sql`：

	CREATE TABLE blog (
	  id BIGINT PRIMARY KEY AUTO_INCREMENT,
	  title VARCHAR(1024),
	  content TEXT
	);

### 配置mysql连接
使用Spring BOOT CLI进行项目生成包含mysql之后，连接mysql所需要的依赖关键便已经由Spring妥善管理。如果尚未建立mysql数据库，可以使用如下命令创建utf8编码的数据库(自行修改`your_db_name`为你的数据库名称)。

	CREATE DATABASE your_db_name DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci; 

对于数据库的连接，我们需要做的则是，在`src/main/resources/application.properties`进行mysql数据库连接的相应配置(自行修改数据库名与用户名、密码)。

	spring.datasource.driverClassName=com.mysql.jdbc.Driver
	spring.datasource.url=jdbc:mysql://localhost/your_db_name?characterEncoding=utf8
	spring.datasource.username=root
	spring.datasource.password=123456

其中`spring.datasource.url`中指定了mysql数据库所在的服务器地址，以及数据库名称，`characterEncoding`表示的是当mysql driver连接mysql时，如何处理字符串编码。

### 启动站点
建立Blog实体，以及BlogRepository接口；使用Spring MVC进行数据控制与展示；与本系列文章[个人网站搭建系列: Spring MVC, JPA, H2数据库](http://blog.csdn.net/jksl007/article/details/44194605)相同，在此不再赘述。

同样可以使用`mvn spring-boot:run`进行站点启动。

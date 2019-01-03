#### mybatis自动生成映射文件

1、在pom文件中添加插件

```xml
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
     <version>1.3.2</version>
    <configuration>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
    </configuration>
</plugin>
```

2、在src/main/resources添加两个配置文件，并且，新建一个文件夹存放数据库驱动jar包。

1. generator.properties，配置数据库链接相关的信息
```
#mysql
spring.datasource.url=jdbc:mysql://localhost:3306/my_db?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
spring.datasource.username=test
spring.datasource.password=test
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

2.generatorConfig.xml，配置自动生成的dao接口、映射文件、do模型等存放位置及命名。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- 配置文件，放在resource目录下即可 -->
    <properties resource="generator.properties"></properties>
    <!--数据库驱动lib放在根目录下 -->
    <classPathEntry location="lib/mysql-connector-java-5.1.41.jar"/>
    <context id="MysqlTables" targetRuntime="MyBatis3">
        <property name="autoDelimitKeywords" value="true"/>
        <!--可以使用``包括字段名，避免字段名与sql保留字冲突报错-->
        <!-- 
	        <property name="beginningDelimiter" value="`"/>
	        <property name="endingDelimiter" value="`"/>
         -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库链接地址账号密码-->
        <jdbcConnection driverClass="${spring.datasource.driver-class-name}" 
        				connectionURL="${spring.datasource.url}" 
        				userId="${spring.datasource.username}"
                        password="${spring.datasource.password}">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!--生成的DO类存放位置-->
        <javaModelGenerator targetPackage="com.xx.dao.dataobj" 
        					targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--生成映射文件存放位置-->
        <sqlMapGenerator targetPackage="com.xx.dao" 
        				 targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!--生成Dao类存放位置-->
        <javaClientGenerator type="XMLMAPPER" 
        					 targetPackage="com.xx.dao" 
        					 targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!--生成对应表及类名 -->
        <table tableName="tb_user" 
        	   domainObjectName="UserDO" 
        	   enableCountByExample="false"
               enableUpdateByExample="false" 
               enableDeleteByExample="false" 
               enableSelectByExample="false"
               selectByExampleQueryId="false">
           <!--mysql 下可取插入的自增型id-->
            <generatedKey column="id" 
            			sqlStatement="JDBC" 
            			identity="true"/>
        </table>
    </context>
</generatorConfiguration>

```

3、将以上配置文件填写完毕后，执行以下maven命令，就能根据数据库表自动生成相应的接口和xml文件。
```
mvn -Dmybatis.generator.overwrite=true mybatis-generator:generate
```
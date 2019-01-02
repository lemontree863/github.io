1. 引入jar包

```xml
        <!-- 不加hibernate-validator，执行UT会抛出异常 无法初始化类MybatisProperties-->
        <dependency>
	  <groupId>org.hibernate</groupId>
	  <artifactId>hibernate-validator</artifactId>
	</dependency>
			
	<dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-test</artifactId>  
            <scope>test</scope>  
	</dependency>
	    
	    <!-- 数据库测试 -->
	 <dependency>
	   <groupId>org.hsqldb</groupId>
	   <artifactId>hsqldb</artifactId>
	   <scope>test</scope>
	 </dependency>
```

2. 准备测试需要的配置文件

![configfile](https://lemontree863.github.io/2019/1/cf.png)

说明下这几个配置文件的作用
- application.properties ： springboot相关的配置
- applicationContext.xml ： 用于配置service层的bean，主要是分为本模块的bean和其他模块的bean,配置上有所区别。
```xml
<!-- 本模块bean -->
<bean id="xxService" class="com.xx.service.impl.XXServiceImpl"/>

<!-- 依赖的其他模块的bean,通过mock -->
<bean id="userService" class="org.mockito.Mockito" factory-method="mock">
		<constructor-arg value="com.xx.service.UserService" />
</bean>
```
- data.sql : 存放表的记录，通过insert语句初始化
- schema.sql : 存放表的创建sql
    
    
    
3. 代码相关

```java
package com.xx.test;

import org.junit.Rule;
import org.junit.rules.ExpectedException;
import org.junit.runner.RunWith;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.annotation.ImportResource;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;

// 测试用例的父类，主要是引入了service层的bean配置和Mapper接口所在包路径
@RunWith(SpringRunner.class)
@SpringBootTest(classes = TestApplication.class)
@Transactional
@Rollback
public abstract class BaseTest {
    @Rule
    public final ExpectedException thrown = ExpectedException.none();
}

@SpringBootApplication
@MapperScan("com.xx.dao")
@ImportResource(value = { "applicationContext.xml" })
class TestApplication {
}


// 测试类
package com.xx.test.service;
import java.util.List;
import javax.annotation.Resource;
import org.junit.Assert;
import org.junit.Test;
import com.xx.BaseTest;

public class XXServiceTest extends BaseTest {

	@Resource(name = "xxService")
	private XXService xxService;

	@Test
	public void testQuery() {
		
	}

}


```

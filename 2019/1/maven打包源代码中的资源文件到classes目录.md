背景
---
今天在跑服务层单元测试是时，抛出一个异常：
> org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)

分析
---


这个异常是mybatis绑定异常，一般找不到Mapper接口对应的xml文件，或者类与xml中的函数名不一致的时候会抛出这个异常，进过仔细检查确认多遍，确定没有以上问题。

后来意识到，可能是因为xml文件与java代码是放在同一个包下面，maven打包的时候，没有把源代码中的资源文件拷贝到target/classes，导致找不到对应的映射文件。

解决
---
方法一：在pom.xml中添加下列代码
```xml
<build>
	<resources>
		<resource>
			<directory>src/main/java</directory>
			<includes>
				<include>**/*.xml</include>
			</includes>
		</resource>
	</resources>
</build>
```

方法二：使用maven-resources-plugin插件
```xml
<plugin>
	<artifactId>maven-resources-plugin</artifactId>
	<executions>
	<execution>
		<id>copy-xmls</id>
		<phase>process-sources</phase>
		<goals>
			<goal>copy-resources</goal>
		</goals>
		<configuration>
			<outputDirectory>${basedir}/target/classes</outputDirectory>
			<resources>
				<resource>
				<directory>${basedir}/src/main/java</directory>
				<includes>
					<include>**/*.xml</include>
				</includes>
				</resource>
			</resources>
		</configuration>
	</execution>
	</executions>
</plugin>
```


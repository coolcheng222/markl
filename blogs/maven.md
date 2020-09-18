---
title: Maven打包安装遇到的问题
tags: [maven,java,早期作品]
date: 2020-9-5 19:06:00
---

1. **找不到主清单**

解决方案: 在pom.xml的插件中指定主类

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>xx.xx(主类全类名)</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
     </plugins>
</build>
```

2. **依赖不出现在jar包里**

解决方案: 使用shade插件

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-shade-plugin</artifactId>
	<version>1.4</version>
    <executions>
		<execution>
			<phase>package</phase>
			<goals>
				<goal>shade</goal>
            </goals>
			<configuration>
				<!-- 只想把jar包引入的话,这里可以什么都不写 -->
			</configuration>
    	</execution>
	</executions>
</plugin>
```

3. **不认识UTF8,不认识自己的jdk**

解决方案: 可以在setting.xml改,在pom里也可以改

```xml
<!-- pom方案 -->
     <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
        <java.version>13</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties> 

```


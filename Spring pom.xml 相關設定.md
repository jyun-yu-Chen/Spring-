# 使用Spring IO platform與Spring Cloud自動替我們引入Spring中各個項目的版本並管理，不用指定版本，保證項目間可以互相兼容

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>io.spring.platform</groupId>
            <artifactId>platform-bom</artifactId>
            <version>Cairo-SR3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-dependencies</artifactId>
    <version>Finchley.SR1</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```



# 引入Maven compiler plugin

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>   
    </plugins>
</build>
```





# 配置打包成一個可執行的war或jar，否則所依賴的函式庫並不會打包進去，只會打包你寫的程式碼，而這樣的打包是無法執行的

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.0.4.RELEASE</version>
            <executions>
          <execution>
            <goals>
              <goal>repackage</goal>
            </goals>
          </execution>
        </executions>
        </plugin>
    </plugins>
    <finalName>igas</finalName>
</build>
```


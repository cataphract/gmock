
```
<build>
    <plugins>
        <!-- the gmaven plugin is needed to compile the tests which have to be written in Groovy -->
        <plugin>
            <groupId>org.codehaus.groovy.maven</groupId>
            <artifactId>gmaven-plugin</artifactId>
            <version>1.0-rc-5</version>
            <executions>
                <execution>
                    <goals>
                        <goal>testCompile</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>

<dependencies>
    <!-- Gmock 0.8.0 needs Groovy 1.6.x where x >= 1 -->
    <dependency>
        <groupId>org.codehaus.groovy</groupId>
        <artifactId>groovy-all</artifactId>
        <version>1.6.1</version>
    </dependency>
    <!-- you can use junit or testng -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.5</version>
        <scope>test</scope>
    </dependency>
    <!-- Gmock 0.8.0 -->
    <dependency>
        <groupId>org.gmock</groupId>
        <artifactId>gmock</artifactId>
        <version>0.8.0</version>
        <scope>test</scope>
    </dependency>
    <!-- optionally, you can use hamcrest matchers -->
    <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest-library</artifactId>
        <version>1.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
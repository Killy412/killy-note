### 宏伟

```xml
<?xml version="1.0" encoding="UTF-8"?>

<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

    <localRepository>/var/local/maven-repo</localRepository>

    <pluginGroups>
        <pluginGroup>org.mortbay.jetty</pluginGroup>
    </pluginGroups>

    <proxies>
    </proxies>

    <servers>
        <server>
            <id>releases</id>
            <username>ali</username>
            <password>ali</password>
        </server>
        <server>
            <id>Snapshots</id>
            <username>ali</username>
            <password>ali</password>
        </server>
    </servers>

    <mirrors>
        <mirror>
            <!--This sends everything else to /public -->
            <id>nexus</id>
            <mirrorOf>*</mirrorOf>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        </mirror>
        <mirror>
            <!--This is used to direct the public snapshots repo in the
                profile below over to a different nexus group -->
            <id>nexus-public-snapshots</id>
            <mirrorOf>*</mirrorOf>
            <url>http://maven.aliyun.com/nexus/content/repositories/snapshots/</url>
        </mirror>
		<mirror>
			<id>central-repos</id>
            <name>Central Repository</name>
            <mirrorOf>*</mirrorOf>
            <url>http://repo1.maven.apache.org/maven2</url>
		</mirror>
		<mirror>
            <id>maven-official-repository</id>
            <name>maven official repository</name>
            <mirrorOf>*</mirrorOf>
            <url>https://mvnrepository.com/artifact/</url>
        </mirror>


        <!--hwidt start-->
        <mirror> 
        		<id>hwidt-snapshots</id>
        		<mirrorOf>snapshots</mirrorOf> 
        		<name>Nexus hwidt snapshots</name>
        		<url>http://119.3.55.191:8089/nexus/content/repositories/hwidt-snapshots/</url> 
        	</mirror>
        	<mirror>
        		<id>hwidt-public</id>
        		<mirrorOf>*</mirrorOf>
        		<name>Maven public</name>
        		 <!--<url>http://119.3.55.191:8089/nexus/content/groups/public/</url>-->
        		<url>http://maven.aliyun.com/nexus/content/groups/public</url>
        		
        	</mirror>
        	<mirror> 
        		<id>hwidt-releases</id>
        		<mirrorOf>releases</mirrorOf> 
        		<name>Nexus releases</name>
        		<url>http://119.3.55.191:8089/nexus/content/repositories/hwidt-releases/</url> 
        	</mirror>
        <!--hwidt end-->
    </mirrors>

    <profiles>
        <!--hwidt start-->
        <profile>
			<id>snapshots</id>
			<repositories>
				<repository>
					<id>snapshots</id>
					<url>http://snapshots</url>
					<releases>
						<enabled>false</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
						<updatePolicy>always</updatePolicy>
					</snapshots>
				</repository>
			</repositories>
			<pluginRepositories>
				<pluginRepository>
					<id>snapshots</id>
					<url>http://snapshots</url>
					<releases>
						<enabled>false</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
					</snapshots>
				</pluginRepository>
			</pluginRepositories>
		</profile>
        <!--hwidt end-->
        <profile>
            <id>development</id>
            <repositories>
                <repository>
                    <id>central</id>
                    <url>http://central</url>
<!--					<url>http://maven.aliyun.com/nexus/content/groups/public/</url>-->
                    <releases>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>central</id>
                    <url>http://central</url>
<!--                    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>-->
                    <releases>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
        <profile>
            <!--this profile will allow snapshots to be searched when activated-->
            <id>public-snapshots</id>
            <repositories>
                <repository>
                    <id>public-snapshots</id>
<!--                    <url>http://public-snapshots</url>-->
                    <url>http://repo1.maven.apache.org/maven2</url>
                    <releases>
                        <enabled>false</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>public-snapshots</id>
<!--                    <url>http://public-snapshots</url>-->
                    <url>http://repo1.maven.apache.org/maven2</url>
                    <releases>
                        <enabled>false</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                        <updatePolicy>always</updatePolicy>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
        <profile>
            <id>jdk-1.8</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
            </activation>
            <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
            </properties>
        </profile>
    </profiles>
    <activeProfiles>
        <activeProfile>development</activeProfile>
        <activeProfile>public-snapshots</activeProfile>
        <activeProfile>snapshots</activeProfile>
    </activeProfiles>
</settings>
```


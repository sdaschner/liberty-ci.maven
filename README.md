ci.maven
========

Collection of Maven plugins and archetypes for managing WebSphere Application Server Liberty Profile and applications.

* [Build](#build)
* [Plugins](#plugins)
 * [liberty-maven-plugin](#liberty-maven-plugin)
     * [Configuration](#configuration)
     * [Goals](#goals)
* [Archetypes](#archetypes)
 * [liberty-plugin-archetype](#liberty-plugin-archetype)

## Build

Use Maven 2.x or 3.x to build the Liberty plugins and archetypes. 

* `mvn install` : builds the plugin and the archetype. 
* `mvn install -DwlpInstallDir=<liberty_install_directory>` : builds the plugin, archetype and runs the integration tests.
  * Liberty Profile installation is required to run the integration tests.

## Plugins

### liberty-maven-plugin

`liberty-maven-plugin` provides a number of goals for managing Liberty Profile server and applications.

#### Configuration

To enable `liberty-maven-plugin` in your project add the following to your `pom.xml`:

    <project>
        ...
        <build>
            <plugins>
                <!-- Enable liberty-maven-plugin -->
                <plugin>
                    <groupId>net.wasdev.wlp.maven.plugins</groupId>
                    <artifactId>liberty-maven-plugin</artifactId> 
                    <version>1.0-SNAPSHOT</version>
                    <!-- Specify configuration, executions for liberty-maven-plugin --> 
                    ...             
                </plugin>
            </plugins>
        </build>
        ...
    </project>

`liberty-maven-plugin` must first be configured with Liberty Profile installation information. The installation information can be specified as an existing installation directory, a compressed archive, or as a Maven artifact. 

* Use the `serverHome` parameter to specify the directory of an existing Liberty Profile server installation. For example:

        <plugin>
            <groupId>net.wasdev.wlp.maven.plugins</groupId>
            <artifactId>liberty-maven-plugin</artifactId> 
            <configuration>
                <serverHome>/opt/ibm/wlp</serverHome>
            </configuration>
        </plugin>

* Use the `assemblyArchive` parameter to specify a compressed archive that contains Liberty Profile server files. For example:

        <plugin>
            <groupId>net.wasdev.wlp.maven.plugins</groupId>
            <artifactId>liberty-maven-plugin</artifactId> 
            <configuration>
                <assemblyArchive>/opt/ibm/wlp.zip</assemblyArchive>
            </configuration>
        </plugin>

* Use the `assemblyArtifact` parameter to specify the name of the Maven artifact that contains Liberty Profile server files. For example:

        <plugin>
            <groupId>net.wasdev.wlp.maven.plugins</groupId>
            <artifactId>liberty-maven-plugin</artifactId> 
            <configuration>
                <assemblyArtifact> 
                    <groupId>net.wasdev.wlp.test</groupId> 
                    <artifactId>liberty-test-server</artifactId>
                    <version>1.0</version>
                    <type>zip</type>
                </assemblyArtifact>         
            </configuration>
        </plugin>

#### Goals

##### Common Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| serverHome | Directory location of the Liberty profile server installation. | Yes, only when `assemblyArchive` and `assemblyArtifact` parameters are not set. |
| assemblyArchive | Location of the Liberty profile server compressed archive. The archive will be unpacked into a directory as specified by the `installDirectory` parameter. | Yes, only when `serverHome` and `assemblyArtifact` parameters are not set. |
| assemblyArtifact | Maven artifact name of the Liberty profile server assembly. The assembly will be installed into a directory as specified by the `installDirectory` parameter. | Yes, only when `serverHome` and `assemblyArchive` parameters are not set. |
| installDirectory | Local installation directory location of the Liberty profile server when the server is installed using the assembly archive or artifact option. The default value is `${project.build.directory}/liberty`.  | No |
| refresh | If true, re-install Liberty profile server into the local directory. This is only used when when the server is installed using the assembly archive or artifact option. The default value is false. | No |
| serverName | Name of the Liberty profile server instance. The default value is `defaultServer`. | No |


##### start-server
---
Start a Liberty Profile server. The server instance will be automatically created if it does not exist.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| configFile | Location of a server configuration file to be used by the instance. The default value is `${basedir}/src/test/resources/server.xml`. | No |
| bootProps | Location of a bootstrap properties file to be used by the instance. The default value is `${basedir}/src/test/resources/bootstrap.properties`. | No |
| jvmOptions | Location of a JVM options file to be used by the instance. The default value is `${basedir}/src/test/resources/jvm.options`. | No |
| serverEnv | Location of a server environment file to be used by the instance. The default value is `${basedir}/src/test/resources/server.env` | No |
| serverStartTimeout | Maximum time to wait (in seconds) to verify that the server has started. The default value is 30 seconds. | No |
| verifyTimeout | Maximum time to wait (in seconds) to verify that the applications have started. This timeout only has effect if the `applications` parameter is set. The default value is 30 seconds. | No |
| applications | A comma-separated list of application names to wait for during server start-up. | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>start-server</id>
                <phase>pre-integration-test</phase>
                <goals>
                    <goal>start-server</goal>
                </goals>
                <configuration>
                    <verifyTimeout>60</verifyTimeout>       
                    <configFile>${project.build.testOutputDirectory}/wlp/server.xml</configFile>     
                </configuration>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

##### stop-server
---
Stop a Liberty Profile server. The server instance must exist and must be running.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| configFile | Location of a server configuration file to be used by the instance. The default value is `${basedir}/src/test/resources/server.xml`. | No |
| bootProps | Location of a bootstrap properties file to be used by the instance. The default value is `${basedir}/src/test/resources/bootstrap.properties`. | No |
| jvmOptions | Location of a JVM options file to be used by the instance. The default value is `${basedir}/src/test/resources/jvm.options`. | No |
| serverEnv | Location of a server environment file to be used by the instance. The default value is `${basedir}/src/test/resources/server.env` | No |
| timeout | Maximum time to wait (in milliseconds) to verify that the server has stopped. The default value is 40 seconds. | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>stop-server</id>
                <phase>post-integration-test</phase>
                <goals>
                    <goal>stop-server</goal>
                </goals>
                <configuration>
                    <timeout>60000</timeout>            
                </configuration>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

##### create-server
---
Create a Liberty Profile server.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| configFile | Location of a server configuration file to be used by the instance. The default value is `${basedir}/src/test/resources/server.xml`. | No |
| bootProps | Location of a bootstrap properties file to be used by the instance. The default value is `${basedir}/src/test/resources/bootstrap.properties`. | No |
| jvmOptions | Location of a JVM options file to be used by the instance. The default value is `${basedir}/src/test/resources/jvm.options`. | No |
| serverEnv | Location of a server environment file to be used by the instance. The default value is `${basedir}/src/test/resources/server.env` | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>create-server</id>
                <phase>pre-integration-test</phase>
                <goals>
                    <goal>create-server</goal>
                </goals>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

##### package-server
---
Package a Liberty Profile server.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| configFile | Location of a server configuration file to be used by the instance. The default value is `${basedir}/src/test/resources/server.xml`. | No |
| bootProps | Location of a bootstrap properties file to be used by the instance. The default value is `${basedir}/src/test/resources/bootstrap.properties`. | No |
| jvmOptions | Location of a JVM options file to be used by the instance. The default value is `${basedir}/src/test/resources/jvm.options`. | No |
| serverEnv | Location of a server environment file to be used by the instance. The default value is `${basedir}/src/test/resources/server.env` | No |
| packageFile | Location of the target file or directory. If the target location is a file, the contents of the server instance will be compressed into the specified file. If the target location is a directory, the contents of the server instance will be compressed into `${packageFile}/${serverName}.zip` file. If the target location is not specified, it defaults to `${serverHome}/usr/servers/${serverName}.zip` if `serverHome` is set. Otherwise, it defaults to `${installDirectory}/usr/servers/${serverName}.zip` if `assemblyArchive` or `assemblyArtifact` is set. | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>package-server</id>
                <phase>package</phase>
                <goals>
                    <goal>package-server</goal>
                </goals>
                <configuration>
                    <packageFile>${project.build.directory}/test.zip</packageFile>                        
                </configuration>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

##### deploy
---
Deploy an application to a Liberty Profile server. The server instance must exist and must be running.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| appArchive | Location of an application file to be deployed. The application type can be war, ear, rar, eba, zip, or jar. | Yes |
| timeout | Maximum time to wait (in milliseconds) to verify that the deployment has completed successfully. The default value is 40 seconds. | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>deploy-app</id>
                <phase>post-integration-test</phase>
                <goals>
                    <goal>deploy</goal>
                </goals>
                <configuration>
                    <appArchive>HelloWorld.war</appArchive>                        
                </configuration>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

##### undeploy
---
Undeploy an application to a Liberty Profile server. The server instance must exist and must be running.

###### Additional Parameters

| Parameter | Description | Required |
| --------  | ----------- | -------  |
| appArchive | Name of an application to be undeployed. The application type can be war, ear, rar, eba, zip, or jar. | Yes |
| timeout | Maximum time to wait (in milliseconds) to verify that the undeployment has completed successfully. The default value is 40 seconds. | No |

Example:

    <plugin>
        <groupId>net.wasdev.wlp.maven.plugins</groupId>
        <artifactId>liberty-maven-plugin</artifactId> 
        <executions>
            ...
            <execution>
                <id>undeploy-app</id>
                <phase>post-integration-test</phase>
                <goals>
                    <goal>undeploy</goal>
                </goals>
                <configuration>
                    <appArchive>HelloWorld.war</appArchive>                        
                </configuration>
            </execution>
            ...
        <executions>
        <configuration>
           <serverHome>/opt/ibm/wlp</serverHome>
           <serverName>test</serverName>
        </configuration>              
    </plugin>

## Archetypes

### liberty-plugin-archetype

`liberty-plugin-archetype` is used to generate a basic multi-module project that builds a simple web application, deploys and tests it on the Liberty Profile server. It also creates a Liberty Profile server package that includes the application.

#### Usage

    mvn archetype:generate \
        -DarchetypeGroupId=net.wasdev.wlp.maven \
        -DarchetypeArtifactId=liberty-plugin-archetype \
        -DgroupId=test \
        -DartifactId=test \
        -Dversion=1.0-SNAPSHOT \
        -DwlpInstallDir=<liberty_install_directory>


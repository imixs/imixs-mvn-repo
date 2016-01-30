# imixs-mvn-repo
The Github Imixs Maven Repository is containg artefacts from the imixs project which are not part of the maven central repository.

#Repo Configuration
When you need to use artefacts from the imixs github repo you need to add this repository into the project pom as follows:

    <repositories>
        <repository>
            <id>imixs-mvn-repo</id>
            <url>https://raw.githubusercontent.com/imixs/imixs-mvn-repo/master/</url>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>


#Maven Deployment

The following section descirbes how to add the maven deploymen configuration into a porject. 

##Authentication
To authenticate against the imixs-mvn-repo add the following server configuration into the maven ~.m2/settings.xml file


    	<!-- Imixs GitHub Repo
    	https://github.com/imixs/imixs-mvn-repo -->
    	<server>
    		<id>imixs-github-repo</id>
    		<username>YOUR_USER_NAME</username>
    		<password>YOUR_GITHUB_PASSWORD</password>
    	</server>
  

##Project Configuration  

The project configuration to enable the deploymen of artefacts into the imixs-github-repo looks like this:


      .....
      	<properties>
      		<!-- imixs-github-repo on github - see ~/.m2/settings.xml -->
          	<github.global.server>imixs-github-repo</github.global.server>
      	</properties>
      	<build>
      		<plugins>
      		.....
      		<!-- Maven Deployment -->
      			<plugin>
      				<artifactId>maven-deploy-plugin</artifactId>
      				<version>2.8.1</version>
      				<configuration>
      					<altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
      				</configuration>
      			</plugin>
      			<plugin>
      				<groupId>com.github.github</groupId>
      				<artifactId>site-maven-plugin</artifactId>
      				<version>0.12</version>
      				<configuration>
      					<!-- git commit message -->
      					<message>Maven artifacts for ${project.version}</message>
      					<!-- disable webpage processing -->
      					<noJekyll>true</noJekyll>
      					<!-- matches distribution management repository url above -->
      					<outputDirectory>${project.build.directory}/mvn-repo</outputDirectory>
      					<!-- remote branch name -->
      					<branch>refs/heads/master</branch>
      					<!-- If you remove this then the old artifact will be removed and new 
      						one will replace. But with the merge tag you can just release by changing 
      						the version -->
      					<merge>true</merge>
      					<includes>
      						<include>**/*</include>
      					</includes>
      					<!-- github repo name -->
      					<repositoryName>imixs-mvn-repo</repositoryName>
      					<!-- github username -->
      					<repositoryOwner>imixs</repositoryOwner>
      				</configuration>
      				<executions>
      					<execution>
      						<goals>
      							<goal>site</goal>
      						</goals>
      						<phase>deploy</phase>
      					</execution>
      				</executions>
      			</plugin>
      ....
      ...


##Deploy Artefacts on GitHub
To deploy the projects artefacts to github run:


	mvn clean deploy 

You should see maven-deploy-plugin create the files to your local staging repository in the target directory, then site-maven-plugin committing those files and pushing them to github. Now the artifact is available to public use.

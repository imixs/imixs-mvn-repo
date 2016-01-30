# imixs-mvn-repo
The imixs-mvn-repo is providing public maven artifacts from the imixs-workflow project, which are not part of the central maven repository. Artifacts will be deployed continuously into this repository by different imixs sub-projects. 

When you need to use artifacts from the imixs-mvn-repo on github, you can add the follwoing repository configuration into the project pom:

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


##Deployment and Project Setup
The following section describes how to add the maven deployment configuration into a imixs subproject. This section is only for developers contributing into the imixs-mvn-repo. If you just want to use artifacts form the repo see the section above.

###Authentication - maven settings.xml
To deploy a new artifact into the imixs-mvn-repo you need to be a contributer for the imixs project. To authenticate against the imixs-mvn-repo add the following server configuration into your maven ~.m2/settings.xml file using your github credentials:

    	<!-- Imixs GitHub Repo
    	https://github.com/imixs/imixs-mvn-repo -->
    	<server>
    		<id>imixs-github-repo</id>
    		<username>YOUR_USER_NAME</username>
    		<password>YOUR_GITHUB_PASSWORD</password>
    	</server>
  

###Project Configuration - maven pom.xml  

The project setup to deployment an artifact into the imixs-github-repo is configured in the project pom.xml and looks like follows:

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

Note: The plugin configuration refers to repo server configuration in the ~.m2/settings.xml

###How to Deploy Artifacts on GitHub
To deploy the projects artifacts to github simply run:

	mvn clean deploy 

You should see maven-deploy-plugin create the files to your local staging repository in the target directory, then site-maven-plugin committing those files and pushing them to github. Now the artifact is available for public use.

See also the following tutorial for more information abut the github plugin:

https://malalanayake.wordpress.com/2014/03/10/create-simple-maven-repository-on-github/

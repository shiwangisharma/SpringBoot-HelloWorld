# SpringBoot-HelloWorld

#############################################################################################################################################

Project Created for the Assessment
Author		: Shiwangi Sharma
Creation Date	: 06/26/2021

#############################################################################################################################################
 
Project contains below details:

	1. Create a simple SpringBoot App using spring-boot-starter-web to print "Hello World on Browser" on local machine.
	
	- Create Jenkins Declarative pipeline to implement below mention points:
		2.Build using Maven by skipping Unit Tests. POM.xml to have all plugins.
		3. Run the app locally on local host: 8080
		4. Package in "Jar"
		5. Publish to Artifactory/Nexus( any other equivalent tool)
		
	6. Once we created the project and jenkins file push the code on github following below commands.
		
		// add ll files from working directory to stagging area
		- git add -A
		
		// add files from staggint to local repository
		- git commit -m "message" 
		
		// psuh files to github
		- git push -u origin main
 
 
    7. Include a README.MD with commands mentioned in all steps  
		7.1 Share the steps to set a maven build in any build tools like Jenkins/Teamcity/Bamboo
		7.2 Share the steps to generate the SSH keys
		7.3 Share the steps to configure any code quality tool like Sonar/PMD in the build  

#############################################################################################################################################

7.1 Share the steps to set a maven build in any build tools like Jenkins/Teamcity/Bamboo

		A) To integrate any tool with jenkins first we need to install plugin, to install plug-in need to goto below path
		
			Manage Jenkins -> Manage Plugins -> Available
			
			 i) Git plugin
			 ii) GitHub Branch Source plugin
			 
			 
		B) Configure the git bin path in Global Tool Configuration.
		
			Manage Jenkins -> Global Tool Configuration -> Maven (Maven Installations)
			
			Name:- Maven_Path
			MAVEN_HOME:- D:\Study_and_Training\DEVOPS_TOOLS\Devops_Softwares\apache-maven-3.3.1\
			
		C) To verify Git integrated properly or not, go to jenkins dashboard and then create new job (like freestyle) then open configure section and under that click on Source code management section.
			And if you are able to see the GIT opetion under SCM then your git plugin is installed.
			Then try to provide the GitHub repository URL and credential and try to build code. In this way verified git integrated with jenkins properly or not.
			https://github.com/shiwangisharma/SpringBoot-HelloWorld.git 
			
		D) You can schdule build with the help of Poll SCM (cron pattern) or web-hook.

#############################################################################################################################################

7.2 Share the steps to generate the SSH keys
		
	A) To create the keys, type:
			ssh-keygen -t rsa
			once you fire the above command after that it will ask for the file in which to save the key.
			
			
	B) Id will be add in authentication agent
			ssh-add
			
	C) If you want to add key on remote machine
			ssh-copy-id username@servername


#############################################################################################################################################

7.3 Share the steps to configure any code quality tool like Sonar/PMD in the build


	A. To integrate any tool with jenkins first we need to install plugin, to install plugin need to goto below path
	
		Manage Jenkins -> Manage Plugins -> Available
	 
		i) SonarQube Scanner
		ii)Sonar Quality Gates
	
	
	B. Configure SonarQube scanner path.
		Manage Jenkins-> Global Configuration Tool -> SonarQube scanner 
		Provide Name: sonar-qube
		SONAR_RUNNER_HOME: /opt/sonar-scanner
	
	
	C. Then need to setup server details in jenkins. 
		
		Manage Jenkins-> Configure System -> SonarQube scanner 
		
		Name:-sonarqube-server
		SonarQube URL:- localhost:8086
		Server authentication token:-  (this token should be created on SonarQube server and then put that token here)
		
			Server authentication token- go to below path on Sonarqube dashboard 
				SonarQube -> security -> user -> Generate Token
				
	D.	One more configuration on SonarQube we have to do(if you want to get some data from SonarQube to Jenkins or some notification, so that why need to configure webhook in SonarQube.) 
			Administration -> Configuration -> Webhooks  

			Name=jenkins
			URL=Jenkins-URL/sonarqube-webhook/
			
	
	E. Once integration done we can use code quality tool in jenkins while create build.  Code also added in Jenkins Pipeline File.
	
			Build will trigger only if its pass quality gates checks. 
					stage('Quality Gates Status Check And Build') {
						steps { 
							script{
									withSonarQubeEnv('sonarqube-server'){
										sh "mvn sonar:sonar"
									}						
									
									timeout(time:1, unit: 'HOURS'){
										def q = waitForQualityGate()
										if(q.status != 'OK'){
										error "Pipeline aborted due to quality gates failure: (q.status)"
										
										}
									
									}
							// Build using Maven by skipping Unit Tests. 			
							bat "mvn -Dmaven.test.skip=true clean package"

							}
						}
					}			
		

#############################################################################################################################################
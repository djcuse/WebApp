
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "deploy"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
  
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/djcuse/webapp.git'
    }
stage ('Sonar Publish') {
	
	withSonarQubeEnv(credentialsId: 'sonarnew') {
    
	sh" mvn -Dsonar.test.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin -Dsonar.tests=. -Dsonar.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.sources=. sonar:sonar -Dsonar.host.url=http://devops-sonar-test.westus.cloudapp.azure.com"	
	
           }	
  }
   stage ('Build App') {	
         sh 'mvn -B -f functionaltest/pom.xml compile'
   }
   
   stage ('Deploy to QA') {	
     deploy adapters: [tomcat7(credentialsId: 'tomcat', 
	path: '', 
	url: 'http://18.219.48.84:8080/')], 
	contextPath: '/QAWebapp', 
	war: '**/*.war'	
   }
		
    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'deploy', snapshotRepo:'deploy-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'deploy', snapshotRepo:'deploy-snapshot', server: server
	  
	    //'Maven build'
	 buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'  
	  
	    //Publish build info'
	   server.publishBuildInfo buildInfo 
    }

  
    stage ('functional Test') {	
	 
          sh 'mvn -B -f functionaltest/pom.xml test'
           publishHTML([allowMissing: false, 
		     alwaysLinkToLastBuild: false, 
		     keepAll: true, 
		     reportDir: "\\functionaltest\\target\\surefire-reports", 
		     reportFiles: 'index.html', 
		     reportName: 'HTML Report', reportTitles: 'functional-testing'])
           }	
	

   stage ('Perf Test Blaze') {		
	 blazeMeterTest credentialsId: 'PerfTest', 
	serverUrl:'https://a.blazemeter.com',
        testId: '7840646',
        workspaceId: '461101'
    }		

	
   stage ('Deploy to Prod') {	
   deploy adapters: [tomcat7(credentialsId: 'tomcat', 
	path: '', 
	url: 'http://3.16.30.55:8080/')], 
	contextPath: '/ProdWebapp', 
	war: '**/*.war'	
}
stage ('Acceptancetest Test') {	
	 
          sh 'mvn -B -f Acceptancetest/pom.xml test'
       publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, 
		 keepAll: false, 
		 reportDir: '\\Acceptancetest\\target\\surefire-reports', 
		 reportFiles: 'index.html', 
		 reportName: 'HTML Report', reportTitles: 'Acceptancetest Test'])
           }
	

    stage ('Slack') {
        // send build started notifications
       // slackSend (color: '#FFFF00')
	  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")  
      }


	
    }
	 

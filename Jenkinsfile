
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
	
   stage ('Build APP') {	
         sh 'mvn -B -f functionaltest/pom.xml compile'
   }

   stage ('Deploy to QA') {	
         sh 'mvn -B -f functionaltest/pom.xml package'	
   }
	
    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'deploy', snapshotRepo:'deploy-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'deploy', snapshotRepo:'deploy-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
    }
   
    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }

    stage ('Test QA') {	
	 
          sh 'mvn -B -f functionaltest/pom.xml test'
           publishHTML([allowMissing: false, 
		     alwaysLinkToLastBuild: false, 
		     keepAll: true, 
		     reportDir: "/var/lib/jenkins/workspace/functional-testing/functionaltest/target/surefire-reports", 
		     reportFiles: 'index.html', 
		     reportName: 'HTML Report', reportTitles: 'functional-testing'])
           }	
	
stage ('Deploy to Prod') {	
         sh 'mvn -B -f Acceptancetest/pom.xml package'	
   }	
	
	
	
	
	
	
	
//stage ('BlazeMeter test'){	
 //   sh "curl -X get https://a.blazemeter.com/api/v4/user --user '94d172a8ddc815ad84e6faa0:3ccbb74937862364652d6e973aa743080762f5c96c67ae9c463e432f6583cc6020608cd6'"
//}
	

	
	
 
	
	
	
	
    stage ('Slack') {
        // send build started notifications
       // slackSend (color: '#FFFF00')
	  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")  
      }

	
	
	
	
	
	
	
    }
	 


node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "deploy"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
  
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/chama1218/webapp.git'
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
	
 stage ('Test') {	
            //sh "mvn -B -f  test"	
	 //bat "mvn -B -f 'functionaltest/pom.xml' test"
	rtMaven.run -B -f 'functionaltest/pom.xml', goals: 'test'
	 //sh 'mvn -B -DskipTests clean package'
        }	
	
    stage ('Slack') {
        // send build started notifications
       // slackSend (color: '#FFFF00')
	  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")  
      }
    }
	 
